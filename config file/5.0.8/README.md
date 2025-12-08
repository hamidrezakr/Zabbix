# **Zabbix Server Configuration Guide**

## **📊 Resource Requirements**

Based on the provided configuration, here are the estimated resource requirements:

### **Minimum System Requirements**

- **RAM**: 6-8 GB (Minimum)
- **CPU**: 4 cores (2.0+ GHz)
- **Disk Space**: 50+ GB (depending on data retention)
- **Swap**: 4 GB recommended

### **Detailed Memory Allocation**

| **Component** | **Memory Allocation** | **Purpose** |
| --- | --- | --- |
| **CacheSize** | 2 GB | Configuration cache (hosts, items, triggers) |
| **HistoryCacheSize** | 1 GB | History data cache |
| **HistoryIndexCacheSize** | 512 MB | History index cache |
| **TrendCacheSize** | 512 MB | Trends data cache |
| **ValueCacheSize** | 2 GB | Item history data requests cache |
| **VMwareCacheSize** | 128 MB | VMware monitoring cache |
| **System & Process** | 1-2 GB | Zabbix processes, OS, database |
| **Total Estimated RAM** | **7-8 GB** | For Zabbix Server processes |

### **CPU Requirements**

- **30 Pollers** - For active checks
- **12 Preprocessors** - For data processing
- **12 Unreachable Pollers** - For unavailable hosts
- **15 Trappers** - For incoming data (agents, senders, proxies)
- **20 Pingers** - For ICMP checks
- **8 Discoverers** - For network discovery
- **15 HTTP Pollers** - For web monitoring
- **4 DB Syncers** - For database synchronization
- **8 LLD Processors** - For Low-Level Discovery
- **1 VMware Collector** - For VMware monitoring

**Total concurrent processes**: ~115 worker processes

**Recommended**: 8+ CPU cores for optimal performance

## **🚀 Performance Capacity**

### **Monitoring Capacity Estimates**

| **Metric** | **Estimated Capacity** |
| --- | --- |
| **Hosts** | 2,000-3,000 hosts |
| **Items** | 100,000-150,000 items |
| **Checks per Second** | 800-1,200 NVPS (New Values Per Second) |
| **Concurrent Connections** | 500-800 connections |
| **Database Operations** | High throughput with 4 DB Syncers |

### **Specific Component Capacities**

1. **Polling Capacity**: 30 pollers × (10s timeout) ≈ 3,000 checks/minute
2. **Trapper Capacity**: 15 trappers handling incoming data
3. **Web Monitoring**: 15 HTTP pollers for web scenarios
4. **Network Discovery**: 8 discoverers for automatic discovery
5. **ICMP Monitoring**: 20 pingers for network availability
6. **Data Processing**: 12 preprocessors for real-time processing

## **⚙️ Configuration Highlights**

### **Database Configuration**

ini

```
DBHost=172.16.164.223
DBName=zabbix
DBUser=zabbix
DBPassword=AjwsoD952vdOou@
```

- **Remote Database**: Configured to connect to external DB server
- **Security**: Password protected with strong credentials
- **Network**: Ensure proper firewall rules for DB connectivity

### **File System Configuration**

ini

```
LogFile=/var/log/zabbix/zabbix_server.log
LogFileSize=0                    # Disable log rotation
PidFile=/var/run/zabbix/zabbix_server.pid
SocketDir=/var/run/zabbix
SNMPTrapperFile=/var/log/snmptrap/snmptrap.log
AlertScriptsPath=/etc/zabbix/alertscripts
ExternalScripts=/etc/zabbix/externalscripts
SSHKeyLocation=/home/zabbix/.ssh
```

### **Performance Optimizations**

1. **Large Cache Sizes**: Aggressive caching strategy for better performance
2. **Worker Process Scaling**: Increased worker counts for high-load environments
3. **Extended Timeout**: `Timeout=10` for slower network environments
4. **Slow Query Logging**: `LogSlowQueries=3000ms` for database performance monitoring
5. **Disabled Log Rotation**: `LogFileSize=0` for continuous logging

### **Monitoring Features Enabled**

- ✅ **VMware Monitoring**: 1 collector with 128MB cache
- ✅ **SNMP Traps**: Configured with custom trap file location
- ✅ **External Scripts**: Custom alert and external scripts support
- ✅ **SSH Checks**: SSH key location configured
- ✅ **ICMP Monitoring**: Fping binary locations specified
- ✅ **Web Monitoring**: 15 HTTP pollers for web scenarios
- ✅ **Low-Level Discovery**: 8 LLD processors for automatic discovery

## **🔧 Important Security Settings**

### **Access Control**

ini

```
StatsAllowedIP=127.0.0.1
```

- **Restricts Stats Access**: Only localhost can access server statistics
- **Production Recommendation**: Add monitoring server IPs if accessing from remote systems

### **File Permissions**

Ensure proper permissions:

bash

```
chown zabbix:zabbix /var/log/zabbix/
chown zabbix:zabbix /var/run/zabbix/
chown zabbix:zabbix /etc/zabbix/alertscripts/
chown zabbix:zabbix /etc/zabbix/externalscripts/
chmod 700 /home/zabbix/.ssh/
```

## **📈 Scaling Recommendations**

### **Vertical Scaling (Increase Resources)**

1. **RAM Increase**: For monitoring more hosts/items
2. **CPU Increase**: For higher NVPS requirements
3. **Cache Tuning**: Adjust cache sizes based on actual usage
4. **Worker Process Adjustment**: Monitor process queue and adjust counts

### **Horizontal Scaling (Distributed Monitoring)**

1. **Use Zabbix Proxies**: For distributed environments
2. **Database Optimization**: Consider PostgreSQL partitioning or TimescaleDB
3. **Frontend Scaling**: Multiple frontend servers with load balancing

## **🚨 Troubleshooting Tips**

### **Common Issues & Solutions**

1. **High Memory Usage**: Monitor cache hit ratios, adjust cache sizes
2. **Slow Performance**: Check `LogSlowQueries` for database bottlenecks
3. **Process Queues**: Monitor Zabbix dashboard for queue lengths
4. **Database Connections**: Ensure DB host is accessible and has enough connections

### **Monitoring the Server**

- Use Zabbix's own templates to monitor the Zabbix server
- Monitor cache utilization (`zabbix[wcache,values]`, `zabbix[wcache,history]`)
- Watch queue sizes in Administration → Queue
- Check database performance metrics

## **📊 Performance Monitoring Metrics**

Key metrics to monitor:

- **NVPS**: New Values Per Second
- **Cache Hit Ratio**: Should be > 80%
- **Queue Size**: Should be minimal
- **Process Busy Rates**: Worker process utilization
- **Database Query Times**: From slow query log

## **🔄 Update Considerations**

When updating Zabbix:

1. **Backup Configuration**: Copy this configuration file
2. **Test in Staging**: Validate configuration with new version
3. **Monitor Performance**: Check for any regression in performance
4. **Adjust Parameters**: New versions may have different optimal settings

---

**Note**: This configuration is optimized for medium to large environments. For small deployments (< 500 hosts), some parameters (cache sizes, worker counts) can be reduced to save resources. Always monitor actual usage and adjust accordingly.



## Contributing
Feel free to:

Fork the repository

Create a feature branch

Submit a pull request

Report issues or suggest improvements

## License
Apache License 2.0

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

### Author
HamidrezaKR

GitHub: @HamidrezaKR

Created: December 2025

### Support
If you find this script useful:

⭐ Star the repository

🐛 Report issues

🔄 Share with others

💡 Suggest improvements
