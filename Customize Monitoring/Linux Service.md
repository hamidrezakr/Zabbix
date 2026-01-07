# **Zabbix Service Monitoring Template**

## **UserParameter Configuration**

### **For any Linux service monitoring:**

```
# Add to /etc/zabbix/zabbix_agent2.conf
UserParameter=service.[*].status,systemctl is-active $1 > /dev/null 2>&1 && echo 1 || echo 0
UserParameter=service.[*].state,systemctl is-active $1
```

**Explanation:**

- `service.[*].status`: Returns `1` if service is running, `0` if stopped
- `service.[*].state`: Returns service state text (active/inactive/failed)
- `$1`: Service name variable (e.g., "clickhouse-server", "nginx", "mysql")

## **Zabbix Server Configuration**

### **Item Creation:**

- **Name**: `[Service Name] Status`
- **Key**: `service.[service_name].status`
- **Type**: `Zabbix agent`
- **Type of information**: `Numeric (unsigned)`
- **Update interval**: `30s`

### **Trigger Configuration:**

- **Name**: `[Service Name] is down`
- **Expression**:

```
{Your_Host_Name:service.[service_name].status.last()}=0
```

- **Severity**: `High`

## **Usage Examples**

### **For clickhouse-server:**

- **Item Key**: `service.clickhouse-server.status`
- **Trigger**: `{Host:service.clickhouse-server.status.last()}=0`

### **For nginx:**

- **Item Key**: `service.nginx.status`
- **Trigger**: `{Host:service.nginx.status.last()}=0`

### **For mysql:**

- **Item Key**: `service.mysql.status`
- **Trigger**: `{Host:service.mysql.status.last()}=0`

## **Testing Command**

```
zabbix_get -s <agent_ip> -k "service.<service_name>.status"
```

**Note:** Replace `<service_name>` with actual systemd service name (use `systemctl list-unit-files` to find exact names).
