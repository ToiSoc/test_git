
## 0.1 📊 磁盘与日志排查速查笔记 

### 0.1.1 查看整体磁盘使用 ```bash df -h


```python
df -h
```

### 0.1.2 查看指定目录下各子目录占用（按大小排序）

```python
du -h --max-depth=1 /path/to/dir | sort -hr
```

### 0.1.3 交互式分析工具（推荐安装）


```python
sudo apt install ncdu
sudo ncdu /          # 分析根目录
ncdu ~/              # 分析家目录
```

### 0.1.4 检查 /var/log 占用

```python
sudo du -sh /var/log/* 2>/dev/null | sort -hr | head -n 20
```

### 0.1.5 安全清理 syslog（关键！不要 rm）

查看现有日志大小

```python
ls -lh /var/log/syslog*
```

```python
# 清空当前日志（保留文件句柄）
sudo truncate -s 0 /var/log/syslog
sudo truncate -s 0 /var/log/syslog.1

# 删除旧轮转日志
sudo rm -f /var/log/syslog.1 /var/log/syslog.*.gz /var/log/syslog.[2-9]

# （可选）重启日志服务
sudo systemctl restart rsyslog
```

### 0.1.6 排查日志爆炸原因

```python
# 查看最近日志
sudo tail -n 100 /var/log/syslog

# 实时监控
sudo tail -f /var/log/syslog

# 检查 systemd journal
sudo journalctl --disk-usage
sudo journalctl --vacuum-size=100M
```

### 0.1.7 长期防护配置

```python
# 强制执行日志轮转
sudo logrotate -f /etc/logrotate.d/rsyslog

# 限制 journald 日志大小（编辑配置）
echo -e "[Journal]\nSystemMaxUse=500M\nRuntimeMaxUse=500M" | sudo tee -a /etc/systemd/journald.conf
sudo systemctl restart systemd-journald
```

### 0.1.8 整理

```python
# 1. 【安全清空】当前正在写入的 syslog（保留文件 inode，服务不中断）
sudo truncate -s 0 /var/log/syslog

# 2. 【删除旧日志】移除轮转备份（.1, .2, .gz 等）
sudo rm -f /var/log/syslog.1 \
          /var/log/syslog.2 \
          /var/log/syslog.*.gz \
          /var/log/syslog.[3-9] \
          /var/log/syslog.[1-9][0-9]

# 3. 【可选】清空其他常见大日志（如 auth.log）
sudo truncate -s 0 /var/log/auth.log
sudo rm -f /var/log/auth.log.* 

# 4. 【清理 journal 日志】（如果使用 systemd-journald）
sudo journalctl --vacuum-size=100M    # 保留最近 100MB
# 或
sudo journalctl --vacuum-time=3d      # 保留最近 3 天

# 5. 【重启日志服务】确保状态正常（通常非必需，但保险起见）
sudo systemctl restart rsyslog

# 6. 【验证清理效果】
df -h /
sudo du -sh /var/log
```







