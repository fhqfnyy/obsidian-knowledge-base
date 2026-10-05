
---
## 🤖 Assistant



# PLC变量读取程序（后台服务）

## 【功能说明】

本程序是一个后台服务应用，实现以下功能：

1. **定时采集** - 按设定周期读取PLC变量数据
2. **配置驱动** - 从数据库读取变量信息和采集配置
3. **多协议支持** - 支持Modbus TCP/OPC UA（可扩展）
4. **数据存储** - 读取的数值实时写入MySQL数据库
5. **断线重连** - 网络异常后自动重连
6. **日志记录** - 运行日志和错误日志

---

## 【设计思路】

```
┌─────────────────────────────────────────────────────────┐
│                    PLC采集服务                           │
├─────────────────────────────────────────────────────────┤
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐  │
│  │ 调度引擎     │───>│ 变量解析器   │───>│ 数据写入器   │  │
│  │ (Timer/Task)│    │ (表达式计算) │    │ (批量写入)   │  │
│  └─────────────┘    └─────────────┘    └─────────────┘  │
│         │                                        │       │
│         ▼                                        ▼       │
│  ┌─────────────────────────────────────────────────┐    │
│  │              PLC通信适配器                        │    │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────┐         │    │
│  │  │ Modbus  │  │ OPC UA  │  │ Siemens │ ...    │    │
│  │  └─────────┘  └─────────┘  └─────────┘         │    │
│  └─────────────────────────────────────────────────┘    │
│                          │                               │
│                          ▼                               │
│  ┌─────────────────────────────────────────────────┐    │
│  │              MySQL 数据库                         │    │
│  │  ┌────────────┐  ┌────────────┐                │    │
│  │  │ 变量配置表  │  │ 数据存储表  │                │    │
│  │  └────────────┘  └────────────┘                │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
```

### 核心组件

| 组件 | 职责 |
|------|------|
| **PlcCollectorService** | 主服务，协调各模块 |
| **PlcCommunicationFactory** | 工厂模式创建通信实例 |
| **ModbusTcpClient** | Modbus TCP协议实现 |
| **OpcUaClient** | OPC UA协议实现 |
| **VariableConfiguration** | 变量配置模型 |
| **MysqlRepository** | 数据库操作 |

---

## 【数据库表结构】

```sql
-- 1. PLC通信配置表
CREATE TABLE `plc_config` (
    `id` INT PRIMARY KEY AUTO_INCREMENT COMMENT '主键ID',
    `plc_name` VARCHAR(50) NOT NULL COMMENT 'PLC名称',
    `protocol_type` VARCHAR(20) NOT NULL COMMENT '协议类型: ModbusTCP, OPCUA, Siemens',
    `ip_address` VARCHAR(50) NOT NULL COMMENT 'IP地址',
    `port` INT NOT NULL DEFAULT 502 COMMENT '端口号',
    `station` INT DEFAULT 1 COMMENT '站号',
    `timeout_ms` INT DEFAULT 3000 COMMENT '超时时间(毫秒)',
    `is_enabled` TINYINT DEFAULT 1 COMMENT '是否启用',
    `remark` VARCHAR(200) DEFAULT NULL COMMENT '备注',
    `create_time` DATETIME DEFAULT CURRENT_TIMESTAMP,
    `update_time` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
) COMMENT='PLC通信配置表';

-- 2. 变量配置表
CREATE TABLE `variable_config` (
    `id` INT PRIMARY KEY AUTO_INCREMENT COMMENT '主键ID',
    `plc_id` INT NOT NULL COMMENT 'PLC配置ID',
    `var_name` VARCHAR(50) NOT NULL COMMENT '变量名称',
    `data_address` VARCHAR(50) NOT NULL COMMENT 'PLC地址(如: 40001, DB1.DBX0.0)',
    `data_type` VARCHAR(20) NOT NULL COMMENT '数据类型: Int16, Int32, UInt16, UInt32, Float, Double, Bool, String',
    `scan_rate_ms` INT DEFAULT 1000 COMMENT '扫描周期(毫秒)',
    `is_enabled` TINYINT DEFAULT 1 COMMENT '是否启用',
    `remark` VARCHAR(200) DEFAULT NULL COMMENT '备注',
    `create_time` DATETIME DEFAULT CURRENT_TIMESTAMP,
    `update_time` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX `idx_plc_id` (`plc_id`),
    FOREIGN KEY (`plc_id`) REFERENCES `plc_config`(`id`)
) COMMENT='变量配置表';

-- 3. 变量数据存储表
CREATE TABLE `variable_data` (
    `id` BIGINT PRIMARY KEY AUTO_INCREMENT COMMENT '主键ID',
    `var_id` INT NOT NULL COMMENT '变量配置ID',
    `var_name` VARCHAR(50) NOT NULL COMMENT '变量名称(冗余存储便于查询)',
    `var_value` VARCHAR(100) NOT NULL COMMENT '变量值',
    `quality` VARCHAR(10) DEFAULT 'GOOD' COMMENT '数据质量: GOOD, BAD, UNCERTAIN',
    `read_time` DATETIME(3) NOT NULL COMMENT '读取时间(毫秒精度)',
    `create_time` DATETIME DEFAULT CURRENT_TIMESTAMP,
    INDEX `idx_var_id_time` (`var_id`, `read_time`),
    INDEX `idx_read_time` (`read_time`)
) COMMENT='变量数据存储表';

-- 4. 服务运行日志表
CREATE TABLE `service_log` (
    `id` BIGINT PRIMARY KEY AUTO_INCREMENT,
    `log_level` VARCHAR(10) NOT NULL COMMENT '日志级别: INFO, WARN, ERROR, DEBUG',
    `log_message` TEXT NOT NULL COMMENT '日志内容',
    `exception_info` TEXT DEFAULT NULL COMMENT '异常信息',
    `create_time` DATETIME DEFAULT CURRENT_TIMESTAMP
) COMMENT='服务运行日志表';

-- 插入示例数据
INSERT INTO `plc_config` (`plc_name`, `protocol_type`, `ip_address`, `port`) VALUES
('主PLC', 'ModbusTCP', '192.168.1.10', 502);

INSERT INTO `variable_config` (`plc_id`, `var_name`, `data_address`, `data_type`, `scan_rate_ms`) VALUES
(1, '温度设定值', '40001', 'Float', 1000),
(1, '实际温度', '40003', 'Float', 500),
(1, '压力值', '40005', 'Float', 1000),
(1, '运行状态', '40007', 'UInt16', 1000);
```

---

## 【完整可运行代码】

### 1. 项目文件结构

```
PlcCollector/
├── appsettings.json          # 配置文件
├── Program.cs                # 程序入口
├── Models/
│   ├── PlcConfiguration.cs
│   ├── VariableConfig.cs
│   └── VariableData.cs
├── Services/
│   ├── IPlcCommunication.cs
│   ├── ModbusTcpCommunication.cs
│   ├── OpcUaCommunication.cs
│   └── PlcCollectorService.cs
├── Repository/
│   └── MysqlRepository.cs
└── PlcCollector.csproj
```

### 2. 配置文件 appsettings.json

```json
{
  "ConnectionStrings": {
    "Default": "Server=localhost;Database=plc_data;Uid=root;Pwd=123456;Charset=utf8mb4;"
  },
  "ServiceConfig": {
    "ScanIntervalMs": 1000,
    "ReconnectIntervalMs": 5000,
    "BatchWriteSize": 100,
    "MaxRetryCount": 3
  },
  "Logging": {
    "LogPath": "logs/plc_collector.log",
    "LogLevel": "INFO"
  }
}
```

### 3. 项目文件 (.csproj)

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <UserSecretsId>dotnet-PlcCollector-xxxxx</UserSecretsId>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Extensions.Hosting" Version="8.0.0" />
    <PackageReference Include="Microsoft.Extensions.Configuration" Version="8.0.0" />
    <PackageReference Include="Microsoft.Extensions.Configuration.Json" Version="8.0.0" />
    <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="8.0.0" />
    <PackageReference Include="Microsoft.Extensions.Logging" Version="8.0.0" />
    <PackageReference Include="MySqlConnector" Version="2.3.1" />
    <PackageReference Include="NModbus4" Version="2.1.0" />
    <PackageReference Include="Opc.UaFx.Client" Version="2.45.0" />
    <PackageReference Include="Serilog" Version="3.1.1" />
    <PackageReference Include="Serilog.Extensions.Hosting" Version="8.0.0" />
    <PackageReference Include="Serilog.Sinks.File" Version="5.0.0" />
  </ItemGroup>
</Project>
```

### 4. 核心代码实现

#### Models/PlcConfiguration.cs

```csharp
namespace PlcCollector.Models;

/// <summary>
/// PLC通信配置
/// </summary>
public class PlcConfiguration
{
    public int Id { get; set; }
    public string PlcName { get; set; } = string.Empty;
    public string ProtocolType { get; set; } = string.Empty;
    public string IpAddress { get; set; } = string.Empty;
    public int Port { get; set; }
    public int Station { get; set; }
    public int TimeoutMs { get; set; } = 3000;
    public bool IsEnabled { get; set; }
    public string? Remark { get; set; }
}
```

#### Models/VariableConfig.cs

```csharp
namespace PlcCollector.Models;

/// <summary>
/// 变量配置
/// </summary>
public class VariableConfig
{
    public int Id { get; set; }
    public int PlcId { get; set; }
    public string VarName { get; set; } = string.Empty;
    public string DataAddress { get; set; } = string.Empty;
    public string DataType { get; set; } = string.Empty;
    public int ScanRateMs { get; set; } = 1000;
    public bool IsEnabled { get; set; }
    public string? Remark { get; set; }
    
    /// <summary>
    /// PLC配置（导航属性）
    /// </summary>
    public PlcConfiguration? PlcConfig { get; set; }
}
```

#### Models/VariableData.cs

```csharp
namespace PlcCollector.Models;

/// <summary>
/// 变量数据
/// </summary>
public class VariableData
{
    public long Id { get; set; }
    public int VarId { get; set; }
    public string VarName { get; set; } = string.Empty;
    public string VarValue { get; set; } = string.Empty;
    public string Quality { get; set; } = "GOOD";
    public DateTime ReadTime { get; set; }
    public DateTime CreateTime { get; set; }
}
```

#### Services/IPlcCommunication.cs

```csharp
using PlcCollector.Models;

namespace PlcCollector.Services;

/// <summary>
/// PLC通信接口
/// </summary>
public interface IPlcCommunication : IDisposable
{
    /// <summary>
    /// 连接PLC
    /// </summary>
    bool Connect();

    /// <summary>
    /// 断开连接
    /// </summary>
    void Disconnect();

    /// <summary>
    /// 读取单个值
    /// </summary>
    /// <param name="address">PLC地址</param>
    /// <param name="dataType">数据类型</param>
    /// <returns>读取结果</returns>
    (string Value, bool Success, string Error) ReadValue(string address, string dataType);

    /// <summary>
    /// 批量读取
    /// </summary>
    Dictionary<string, (string Value, bool Success, string Error)> BatchRead(
        IEnumerable<VariableConfig> variables);

    /// <summary>
    /// 是否已连接
    /// </summary>
    bool IsConnected { get; }

    /// <summary>
    /// 最后错误信息
    /// </summary>
    string? LastError { get; }
}
```

#### Services/ModbusTcpCommunication.cs

```csharp
using NModbus;
using PlcCollector.Models;
using System.Net.Sockets;

namespace PlcCollector.Services;

/// <summary>
/// Modbus TCP通信实现
/// </summary>
public class ModbusTcpCommunication : IPlcCommunication
{
    private readonly PlcConfiguration _config;
    private TcpClient? _tcpClient;
    private IModbusMaster? _master;
    private readonly object _lock = new();
    private string? _lastError;

    public bool IsConnected => _master != null && _tcpClient?.Connected == true;
    
    public string? LastError => _lastError;

    public ModbusTcpCommunication(PlcConfiguration config)
    {
        _config = config;
    }

    public bool Connect()
    {
        lock (_lock)
        {
            try
            {
                Disconnect();

                _tcpClient = new TcpClient();
                _tcpClient.ReceiveTimeout = _config.TimeoutMs;
                _tcpClient.SendTimeout = _config.TimeoutMs;
                _tcpClient.Connect(_config.IpAddress, _config.Port);

                var factory = new ModbusFactory();
                _master = factory.CreateMaster(_tcpClient);
                
                _lastError = null;
                return true;
            }
            catch (Exception ex)
            {
                _lastError = $"Modbus连接失败: {ex.Message}";
                return false;
            }
        }
    }

    public void Disconnect()
    {
        lock (_lock)
        {
            _master?.Dispose();
            _master = null;
            _tcpClient?.Close();
            _tcpClient?.Dispose();
            _tcpClient = null;
        }
    }

    public (string Value, bool Success, string Error) ReadValue(string address, string dataType)
    {
        try
        {
            if (!IsConnected)
            {
                if (!Connect())
                    return (string.Empty, false, _lastError ?? "连接失败");
            }

            var (startAddress, functionCode) = ParseModbusAddress(address);
            var result = ReadFromPlc(startAddress, functionCode, dataType);
            
            return (result, true, string.Empty);
        }
        catch (Exception ex)
        {
            return (string.Empty, false, $"读取失败: {ex.Message}");
        }
    }

    public Dictionary<string, (string Value, bool Success, string Error)> BatchRead(
        IEnumerable<VariableConfig> variables)
    {
        var results = new Dictionary<string, (string Value, bool Success, string Error)>();

        if (!IsConnected && !Connect())
        {
            foreach (var v in variables)
                results[v.DataAddress] = (string.Empty, false, _lastError ?? "连接失败");
            return results;
        }

        // 按地址分组读取优化
        var addressGroups = variables
            .GroupBy(v => ParseModbusAddress(v.DataAddress).startAddress)
            .ToList();

        foreach (var group in addressGroups)
        {
            var firstVar = group.First();
            var (startAddress, functionCode) = ParseModbusAddress(firstVar.DataAddress);

            try
            {
                // 一次性读取多个寄存器
                var count = group.Sum(v => GetDataTypeLength(v.DataType));
                var values = ReadRegisters(startAddress, functionCode, count);
                var offset = 0;

                foreach (var variable in group)
                {
                    var length = GetDataTypeLength(variable.DataType);
                    var bytes = new byte[length * 2];
                    Array.Copy(values, offset * 2, bytes, 0, length * 2);
                    
                    var value = ConvertDataType(bytes, variable.DataType);
                    results[variable.DataAddress] = (value, true, string.Empty);
                    offset += length;
                }
            }
            catch (Exception ex)
            {
                foreach (var variable in group)
                {
                    results[variable.DataAddress] = (string.Empty, false, ex.Message);
                }
            }
        }

        return results;
    }

    private (int startAddress, byte functionCode) ParseModbusAddress(string address)
    {
        // 支持格式: 40001 (Holding Register), 30001 (Input Register)
        // 或者 40001.2 (带小数点位)
        address = address.Trim().ToUpper();

        return address[0] switch
        {
            '4' => (int.Parse(address[1..]) - 1, 3), // Holding Registers (FC03)
            '3' => (int.Parse(address[1..]) - 1, 4), // Input Registers (FC04)
            '0' => address.StartsWith("0X") || address.StartsWith("0x")
                ? (Convert.ToInt32(address[2..], 16), 1) // Coil (FC01)
                : (int.Parse(address) - 1, 1),
            '1' => (int.Parse(address) - 1, 2), // Discrete Input (FC02)
            _ => (int.Parse(address) - 1, 3)
        };
    }

    private ushort[] ReadFromPlc(int startAddress, byte functionCode, string dataType)
    {
        var count = GetDataTypeLength(dataType);
        return functionCode switch
        {
            3 => _master!.ReadHoldingRegisters((byte)_config.Station, (ushort)startAddress, (ushort)count),
            4 => _master!.ReadInputRegisters((byte)_config.Station, (ushort)startAddress, (ushort)count),
            _ => throw new NotSupportedException($"不支持的功能码: {functionCode}")
        };
    }

    private ushort[] ReadRegisters(int startAddress, byte functionCode, int count)
    {
        return ReadFromPlc(startAddress, functionCode, "Int16");
    }

    private int GetDataTypeLength(string dataType)
    {
        return dataType.ToLower() switch
        {
            "bool" => 1,
            "int16" or "uint16" => 1,
            "int32" or "uint32" or "float" => 2,
            "int64" or "uint64" or "double" => 4,
            "string" => 10, // 默认10个寄存器
            _ => throw new NotSupportedException($"不支持的数据类型: {dataType}")
        };
    }

    private string ConvertDataType(byte[] bytes, string dataType)
    {
        return dataType.ToLower() switch
        {
            "bool" => (bytes[0] & 0x80) != 0 ? "1" : "0",
            "int16" => BitConverter.ToInt16(bytes.Reverse().ToArray(), 0).ToString(),
            "uint16" => BitConverter.ToUInt16(bytes.Reverse().ToArray(), 0).ToString(),
            "int32" => BitConverter.ToInt32(bytes.Reverse().ToArray(), 0).ToString(),
            "uint32" => BitConverter.ToUInt32(bytes.Reverse().ToArray(), 0).ToString(),
            "float" => BitConverter.ToSingle(bytes.Reverse().ToArray(), 0).ToString("F4"),
            "double" => BitConverter.ToDouble(bytes.Reverse().ToArray(), 0).ToString("F6"),
            "string" => System.Text.Encoding.ASCII.GetString(bytes).Trim('\0'),
            _ => BitConverter.ToString(bytes)
        };
    }

    public void Dispose()
    {
        Disconnect();
        GC.SuppressFinalize(this);
    }
}
```

#### Services/OpcUaCommunication.cs

```csharp
using Opc.UaFx;
using Opc.UaFx.Client;
using PlcCollector.Models;

namespace PlcCollector.Services;

/// <summary>
/// OPC UA通信实现
/// </summary>
public class OpcUaCommunication : IPlcCommunication
{
    private readonly PlcConfiguration _config;
    private OpcClient? _client;
    private string? _lastError;

    public bool IsConnected => _client?.State == OpcClientState.Connected;

    public string? LastError => _lastError;

    public OpcUaCommunication(PlcConfiguration config)
    {
        _config = config;
    }

    public bool Connect()
    {
        try
        {
            var endpoint = $"opc.tcp://{_config.IpAddress}:{_config.Port}";
            _client = new OpcClient(endpoint);
            _client.Timeout = _config.TimeoutMs;
            _client.Connect();
            
            _lastError = null;
            return true;
        }
        catch (Exception ex)
        {
            _lastError = $"OPC UA连接失败: {ex.Message}";
            return false;
        }
    }

    public void Disconnect()
    {
        _client?.Disconnect();
        _client?.Dispose();
        _client = null;
    }

    public (string Value, bool Success, string Error) ReadValue(string address, string dataType)
    {
        try
        {
            if (_client == null || !IsConnected)
            {
                if (!Connect())
                    return (string.Empty, false, _lastError ?? "连接失败");
            }

            var nodeId = address.StartsWith("ns=", StringComparison.OrdinalIgnoreCase) 
                ? OpcNodeId.Parse(address) 
                : new OpcDataVariableNode(address);

            var value = _client!.ReadNode(nodeId);
            return (value?.ToString() ?? string.Empty, true, string.Empty);
        }
        catch (Exception ex)
        {
            return (string.Empty, false, $"读取失败: {ex.Message}");
        }
    }

    public Dictionary<string, (string Value, bool Success, string Error)> BatchRead(
        IEnumerable<VariableConfig> variables)
    {
        var results = new Dictionary<string, (string Value, bool Success, string Error)>();

        if ((_client == null || !IsConnected) && !Connect())
        {
            foreach (var v in variables)
                results[v.DataAddress] = (string.Empty, false, _lastError ?? "连接失败");
            return results;
        }

        try
        {
            var nodeIds = variables
                .Select(v => v.DataAddress.StartsWith("ns=", StringComparison.OrdinalIgnoreCase)
                    ? OpcNodeId.Parse(v.DataAddress)
                    : new OpcDataVariableNode(v.DataAddress))
                .ToArray();

            var readResults = _client!.ReadNodes(nodeIds);

            var index = 0;
            foreach (var variable in variables)
            {
                var result = readResults[index++];
                results[variable.DataAddress] = result.IsGood
                    ? (result.Value?.ToString() ?? string.Empty, true, string.Empty)
                    : (string.Empty, false, result.Status?.ToString() ?? "未知错误");
            }
        }
        catch (Exception ex)
        {
            foreach (var variable in variables)
                results[variable.DataAddress] = (string.Empty, false, ex.Message);
        }

        return results;
    }

    public void Dispose()
    {
        Disconnect();
        GC.SuppressFinalize(this);
    }
}
```

#### Services/PlcCommunicationFactory.cs

```csharp
using PlcCollector.Models;

namespace PlcCollector.Services;

/// <summary>
/// PLC通信工厂
/// </summary>
public class PlcCommunicationFactory
{
    public static IPlcCommunication Create(PlcConfiguration config)
    {
        return config.ProtocolType.ToUpperInvariant() switch
        {
            "MODBUSTCP" or "MODBUS" => new ModbusTcpCommunication(config),
            "OPCU" or "OPCUA" => new OpcUaCommunication(config),
            _ => throw new NotSupportedException($"不支持的协议类型: {config.ProtocolType}")
        };
    }
}
```

#### Repository/MysqlRepository.cs

```csharp
using Microsoft.Extensions.Logging;
using MySqlConnector;
using PlcCollector.Models;
using System.Collections.Concurrent;

namespace PlcCollector.Repository;

/// <summary>
/// MySQL数据访问层
/// </summary>
public class MysqlRepository
{
    private readonly string _connectionString;
    private readonly ILogger<MysqlRepository> _logger;
    private readonly ConcurrentBag<VariableData> _batchBuffer = new();
    private readonly object _flushLock = new();

    public MysqlRepository(string connectionString, ILogger<MysqlRepository> logger)
    {
        _connectionString = connectionString;
        _logger = logger;
    }

    /// <summary>
    /// 获取所有启用的PLC配置
    /// </summary>
    public async Task<List<PlcConfiguration>> GetPlcConfigurationsAsync()
    {
        const string sql = @"
            SELECT id, plc_name, protocol_type, ip_address, port, station, 
                   timeout_ms, is_enabled, remark
            FROM plc_config 
            WHERE is_enabled = 1";

        return await QueryAsync<PlcConfiguration>(sql);
    }

    /// <summary>
    /// 获取指定PLC的启用的变量配置
    /// </summary>
    public async Task<List<VariableConfig>> GetVariableConfigsAsync(int plcId)
    {
        const string sql = @"
            SELECT id, plc_id, var_name, data_address, data_type, 
                   scan_rate_ms, is_enabled, remark
            FROM variable_config 
            WHERE plc_id = @PlcId AND is_enabled = 1";

        await using var connection = new MySqlConnection(_connectionString);
        await connection.OpenAsync();

        var variables = new List<VariableConfig>();
        await using var command = new MySqlCommand(sql, connection);
        command.Parameters.AddWithValue("@PlcId", plcId);

        await using var reader = await command.ExecuteReaderAsync();
        while (await reader.ReadAsync())
        {
            variables.Add(new VariableConfig
            {
                Id = reader.GetInt32("id"),
                PlcId = reader.GetInt32("plc_id"),
                VarName = reader.GetString("var_name"),
                DataAddress = reader.GetString("data_address"),
                DataType = reader.GetString("data_type"),
                ScanRateMs = reader.GetInt32("scan_rate_ms"),
                IsEnabled = reader.GetBoolean("is_enabled"),
                Remark = reader.IsDBNull("remark") ? null : reader.GetString("remark")
            });
        }

        return variables;
    }

    /// <summary>
    /// 批量写入数据
    /// </summary>
    public async Task BatchInsertDataAsync(IEnumerable<VariableData> dataList)
    {
        var dataArray = dataList.ToArray();
        if (dataArray.Length == 0) return;

        const string sql = @"
            INSERT INTO variable_data (var_id, var_name, var_value, quality, read_time, create_time)
            VALUES (@VarId, @VarName, @VarValue, @Quality, @ReadTime, @CreateTime)";

        await using var connection = new MySqlConnection(_connectionString);
        await connection.OpenAsync();

        await using var transaction = await connection.BeginTransactionAsync();
        try
        {
            await using var command = new MySqlCommand(sql, connection, transaction);
            
            foreach (var data in dataArray)
            {
                command.Parameters.Clear();
                command.Parameters.AddWithValue("@VarId", data.VarId);
                command.Parameters.AddWithValue("@VarName", data.VarName);
                command.Parameters.AddWithValue("@VarValue", data.VarValue);
                command.Parameters.AddWithValue("@Quality", data.Quality);
                command.Parameters.AddWithValue("@ReadTime", data.ReadTime);
                command.Parameters.AddWithValue("@CreateTime", data.CreateTime);
                
                await command.ExecuteNonQueryAsync();
            }

            await transaction.CommitAsync();
        }
        catch (Exception ex)
        {
            await transaction.RollbackAsync();
            _logger.LogError(ex, "批量写入数据失败");
            throw;
        }
    }

    /// <summary>
    /// 记录日志
    /// </summary>
    public async Task InsertLogAsync(string level, string message, string? exceptionInfo = null)
    {
        const string sql = @"
            INSERT INTO service_log (log_level, log_message, exception_info)
            VALUES (@Level, @Message, @ExceptionInfo)";

        try
        {
            await using var connection = new MySqlConnection(_connectionString);
            await connection.OpenAsync();

            await using var command = new MySqlCommand(sql, connection);
            command.Parameters.AddWithValue("@Level", level);
            command.Parameters.AddWithValue("@Message", message);
            command.Parameters.AddWithValue("@ExceptionInfo", exceptionInfo);

            await command.ExecuteNonQueryAsync();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "写入日志失败");
        }
    }

    private async Task<List<T>> QueryAsync<T>(string sql) where T : new()
    {
        await using var connection = new MySqlConnection(_connectionString);
        await connection.OpenAsync();

        var result = new List<T>();
        await using var command = new MySqlCommand(sql, connection);

        await using var reader = await command.ExecuteReaderAsync();
        while (await reader.ReadAsync())
        {
            var item = new T();
            foreach (var prop in typeof(T).GetProperties())
            {
                var ordinal = reader.GetOrdinal(prop.Name);
                if (!await reader.IsDBNullAsync(ordinal))
                {
                    var value = await reader.GetValueAsync(ordinal);
                    prop.SetValue(item, Convert.ChangeType(value, prop.PropertyType));
                }
            }
            result.Add(item);
        }

        return result;
    }
}
```

#### Services/PlcCollectorService.cs

```csharp
using PlcCollector.Models;
using PlcCollector.Repository;

namespace PlcCollector.Services;

/// <summary>
/// PLC采集服务核心类
/// </summary>
public class PlcCollectorService
{
    private readonly MysqlRepository _repository;
    private readonly ILogger<PlcCollectorService> _logger;
    private readonly IServiceConfiguration _config;
    private readonly Dictionary<int, IPlcCommunication> _connections = new();
    private readonly Dictionary<int, List<VariableConfig>> _variablesCache = new();
    private readonly SemaphoreSlim _collectionLock = new(1, 1);
    private CancellationTokenSource? _cts;

    public PlcCollectorService(
        MysqlRepository repository,
        ILogger<PlcCollectorService> logger,
        IServiceConfiguration config)
    {
        _repository = repository;
        _logger = logger;
        _config = config;
    }

    /// <summary>
    /// 启动服务
    /// </summary>
    public async Task StartAsync(CancellationToken cancellationToken = default)
    {
        _logger.LogInformation("PLC采集服务启动中...");
        _cts = new CancellationTokenSource();

        try
        {
            // 加载配置
            await ReloadConfigurationAsync();

            // 记录日志
            await _repository.InsertLogAsync("INFO", "PLC采集服务启动");

            // 启动采集任务
            _ = Task.Run(() => CollectionLoopAsync(_cts.Token), CancellationToken.None);

            _logger.LogInformation("PLC采集服务已启动");
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "启动PLC采集服务失败");
            await _repository.InsertLogAsync("ERROR", $"启动服务失败: {ex.Message}", ex.ToString());
            throw;
        }
    }

    /// <summary>
    /// 停止服务
    /// </summary>
    public async Task StopAsync()
    {
        _logger.LogInformation("PLC采集服务停止中...");
        _cts?.Cancel();

        // 关闭所有连接
        foreach (var connection in _connections.Values)
        {
            connection.Disconnect();
        }
        _connections.Clear();
        _variablesCache.Clear();

        await _repository.InsertLogAsync("INFO", "PLC采集服务已停止");
        _logger.LogInformation("PLC采集服务已停止");
    }

    /// <summary>
    /// 重新加载配置
    /// </summary>
    public async Task ReloadConfigurationAsync()
    {
        await _collectionLock.WaitAsync();
        try
        {
            _logger.LogInformation("重新加载配置...");

            // 获取PLC配置
            var plcConfigs = await _repository.GetPlcConfigurationsAsync();

            // 清理旧的连接
            var newPlcIds = plcConfigs.Select(p => p.Id).ToHashSet();
            foreach (var plcId in _connections.Keys.Where(id => !newPlcIds.Contains(id)).ToList())
            {
                _connections[plcId]?.Dispose();
                _connections.Remove(plcId);
                _variablesCache.Remove(plcId);
            }

            // 建立新连接并加载变量
            foreach (var plcConfig in plcConfigs)
            {
                if (!_connections.ContainsKey(plcConfig.Id))
                {
                    _connections[plcConfig.Id] = PlcCommunicationFactory.Create(plcConfig);
                    
                    // 尝试连接
                    if (_connections[plcConfig.Id].Connect())
                    {
                        _logger.LogInformation("已连接PLC: {PlcName}", plcConfig.PlcName);
                    }
                    else
                    {
                        _logger.LogWarning("连接PLC失败: {PlcName} - {Error}", 
                            plcConfig.PlcName, _connections[plcConfig.Id].LastError);
                    }
                }

                // 加载变量配置
                _variablesCache[plcConfig.Id] = await _repository.GetVariableConfigsAsync(plcConfig.Id);
                _logger.LogInformation("PLC {PlcName} 加载了 {Count} 个变量", 
                    plcConfig.PlcName, _variablesCache[plcConfig.Id].Count);
            }

            await _repository.InsertLogAsync("INFO", $"重新加载配置完成，共{plcConfigs.Count}个PLC");
        }
        finally
        {
            _collectionLock.Release();
        }
    }

    /// <summary>
    /// 采集循环
    /// </summary>
    private async Task CollectionLoopAsync(CancellationToken ct)
    {
        var scanInterval = _config.ScanIntervalMs;
        var lastReconnect = DateTime.MinValue;

        while (!ct.IsCancellationRequested)
        {
            try
            {
                // 检查是否需要重连
                if (DateTime.Now - lastReconnect > TimeSpan.FromSeconds(_config.ReconnectIntervalMs / 1000))
                {
                    await CheckAndReconnectAsync();
                    lastReconnect = DateTime.Now;
                }

                // 执行数据采集
                await CollectDataAsync();

                // 等待下一个采集周期
                await Task.Delay(scanInterval, ct);
            }
            catch (OperationCanceledException) when (ct.IsCancellationRequested)
            {
                break;
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "采集循环发生错误");
                await Task.Delay(1000, ct); // 短暂等待后继续
            }
        }
    }

    /// <summary>
    /// 检查并重连断开的PLC
    /// </summary>
    private async Task CheckAndReconnectAsync()
    {
        foreach (var (plcId, connection) in _connections)
        {
            if (!connection.IsConnected)
            {
                _logger.LogWarning("PLC {PlcId} 断开连接，尝试重连...", plcId);
                await _repository.InsertLogAsync("WARN", $"PLC {plcId} 断开连接，尝试重连");

                if (!connection.Connect())
                {
                    _logger.LogError("PLC {PlcId} 重连失败: {Error}", plcId, connection.LastError);
                }
                else
                {
                    _logger.LogInformation("PLC {PlcId} 重连成功", plcId);
                    await _repository.InsertLogAsync("INFO", $"PLC {plcId} 重连成功");
                }
            }
        }
    }

    /// <summary>
    /// 执行数据采集
    /// </summary>
    private async Task CollectDataAsync()
    {
        await _collectionLock.WaitAsync();
        try
        {
            var allData = new List<VariableData>();
            var readTime = DateTime.Now;

            foreach (var (plcId, variables) in _variablesCache)
            {
                if (!_connections.TryGetValue(plcId, out var connection))
                {
                    _logger.LogWarning("找不到PLC {PlcId} 的连接", plcId);
                    continue;
                }

                if (!connection.IsConnected)
                {
                    continue;
                }

                try
                {
                    var results = connection.BatchRead(variables);

                    foreach (var variable in variables)
                    {
                        var quality = "BAD";
                        if (results.TryGetValue(variable.DataAddress, out var result))
                        {
                            quality = result.Success ? "GOOD" : "BAD";
                            
                            if (result.Success)
                            {
                                allData.Add(new VariableData
                                {
                                    VarId = variable.Id,
                                    VarName = variable.VarName,
                                    VarValue = result.Value,
                                    Quality = quality,
                                    ReadTime = readTime,
                                    CreateTime = readTime
                                });
                            }
                            else
                            {
                                _logger.LogDebug("读取变量失败: {VarName} - {Error}", 
                                    variable.VarName, result.Error);
                            }
                        }
                    }
                }
                catch (Exception ex)
                {
                    _logger.LogError(ex, "采集PLC {PlcId} 数据失败", plcId);
                }
            }

            // 批量写入数据库
            if (allData.Count > 0)
            {
                await BatchWriteDataAsync(allData);
            }
        }
        finally
        {
            _collectionLock.Release();
        }
    }

    /// <summary>
    /// 批量写入数据
    /// </summary>
    private async Task BatchWriteDataAsync(List<VariableData> dataList)
    {
        try
        {
            await _repository.BatchInsertDataAsync(dataList);
            _logger.LogDebug("成功写入 {Count} 条数据", dataList.Count);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "批量写入数据失败");
            await _repository.InsertLogAsync("ERROR", $"批量写入数据失败: {ex.Message}", ex.ToString());
        }
    }
}

/// <summary>
/// 服务配置接口
/// </summary>
public interface IServiceConfiguration
{
    int ScanIntervalMs { get; }
    int ReconnectIntervalMs { get; }
    int BatchWriteSize { get; }
    int MaxRetryCount { get; }
}

/// <summary>
/// 服务配置实现
/// </summary>
public class ServiceConfiguration : IServiceConfiguration
{
    public int ScanIntervalMs { get; set; } = 1000;
    public int ReconnectIntervalMs { get; set; } = 5000;
    public int BatchWriteSize { get; set; } = 100;
    public int MaxRetryCount { get; set; } = 3;
}
```

#### Program.cs

```csharp
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using PlcCollector.Repository;
using PlcCollector.Services;
using Serilog;

namespace PlcCollector;

internal class Program
{
    private static async Task Main(string[] args)
    {
        // 配置Serilog日志
        Log.Logger = new LoggerConfiguration()
            .MinimumLevel.Debug()
            .WriteTo.Console()
            .WriteTo.File("logs/plc_collector_.txt", rollingInterval: RollingInterval.Day)
            .CreateLogger();

        try
        {
            var host = Host.CreateDefaultBuilder(args)
                .UseSerilog()
                .ConfigureServices((context, services) =>
                {
                    // 配置
                    services.AddSingleton<IServiceConfiguration>(sp =>
                    {
                        var config = context.Configuration.GetSection("ServiceConfig");
                        return new ServiceConfiguration
                        {
                            ScanIntervalMs = config.GetValue<int>("ScanIntervalMs", 1000),
                            ReconnectIntervalMs = config.GetValue<int>("ReconnectIntervalMs", 5000),
                            BatchWriteSize = config.GetValue<int>("BatchWriteSize", 100),
                            MaxRetryCount = config.GetValue<int>("MaxRetryCount", 3)
                        };
                    });

                    // MySQL仓储
                    services.AddSingleton(sp =>
                    {
                        var connectionString = context.Configuration
                            .GetConnectionString("Default")
                            ?? throw new InvalidOperationException("未配置数据库连接字符串");
                        
                        var logger = sp.GetRequiredService<ILogger<MysqlRepository>>();
                        return new MysqlRepository(connectionString, logger);
                    });

                    // PLC采集服务
                    services.AddSingleton<PlcCollectorService>();
                    services.AddHostedService<PlcCollectorHostedService>();
                })
                .Build();

            await host.RunAsync();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "应用程序启动失败");
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}

/// <summary>
/// 托管服务包装
/// </summary>
public class PlcCollectorHostedService : BackgroundService
{
    private readonly PlcCollectorService _collectorService;
    private readonly ILogger<PlcCollectorHostedService> _logger;

    public PlcCollectorHostedService(
        PlcCollectorService collectorService,
        ILogger<PlcCollectorHostedService> logger)
    {
        _collectorService = collectorService;
        _logger = logger;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        await _collectorService.StartAsync(stoppingToken);

        // 保持运行直到取消
        while (!stoppingToken.IsCancellationRequested)
        {
            await Task.Delay(TimeSpan.FromMinutes(1), stoppingToken);
            
            // 可选：定时刷新配置
            try
            {
                await _collectorService.ReloadConfigurationAsync();
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "刷新配置失败");
            }
        }
    }

    public override async Task StopAsync(CancellationToken cancellationToken)
    {
        _logger.LogInformation("正在停止服务...");
        await _collectorService.StopAsync();
        await base.StopAsync(cancellationToken);
    }
}
```

---

## 【运行配置】

### 1. 安装为Windows服务

```powershell
# 发布项目
dotnet publish -c Release -o publish

# 安装服务
sc create PlcCollector binPath= "C:\PlcCollector\publish\PlcCollector.exe"

# 启动服务
sc start PlcCollector

# 查看服务状态
sc query PlcCollector

# 停止服务
sc stop PlcCollector

# 删除服务
sc delete PlcCollector
```

### 2. 使用NSSM（非服务托管）

```powershell
# 下载NSSM后
nssm install PlcCollector "C:\PlcCollector\publish\PlcCollector.exe"
nssm set PlcCollector AppDirectory "C:\PlcCollector\publish"
nssm set PlcCollector DisplayName "PLC数据采集服务"
nssm set PlcCollector Description "采集PLC数据并存储到MySQL数据库"
nssm set PlcCollector Start SERVICE_AUTO_START
nssm set PlcCollector AppStdout "C:\PlcCollector\logs\stdout.log"
nssm set PlcCollector AppStderr "C:\PlcCollector\logs\stderr.log"
```

---

## 【注意事项与扩展建议】

### 注意事项

| 项目 | 说明 |
|------|------|
| **网络稳定性** | 生产环境建议配置网络监控，自动重连机制已包含 |
| **数据库性能** | 高频采集时考虑使用批量写入，表分区或历史归档 |
| **内存管理** | 长时间运行注意内存泄漏，定期重启或使用内存分析工具 |
| **时区处理** | MySQL使用`DATETIME(3)`存储毫秒，服务器时区需统一 |

### 扩展建议

1. **支持更多协议**
   ```csharp
   // 扩展工厂
   public static IPlcCommunication Create(PlcConfiguration config)
   {
       return config.ProtocolType.ToUpperInvariant() switch
       {
           // 现有协议...
           "SIEMENS" => new SiemensS7Communication(config),
           "MITSUBISHI" => new MitsubishiCommunication(config),
           _ => throw new NotSupportedException(...)
       };
   }
   ```

2. **数据压缩存储**
   ```csharp
   // 只存储变化值（差值压缩）
   if (lastValue != currentValue)
   {
       // 写入数据库
   }
   ```

3. **配置热更新**
   ```csharp
   // 监听配置表变化，自动ReloadConfigurationAsync()
   // 可使用数据库轮询或变更通知
   ```

4. ** Prometheus监控集成**
   ```csharp
   // 暴露采集速率、PLC连接状态等指标
   // 使用Prometheus.Net或OpenTelemetry
   ```

5. **异常告警**
   ```csharp
   // 连续采集失败N次后发送告警（邮件/钉钉/企业微信）
   ```

---

如需进一步定制具体PLC协议实现（如西门子S7、三菱MC协议等），或添加Web管理界面，请告知具体需求！
