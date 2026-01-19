# MySQL数据同步使用指南

## 概述
本系统提供MySQL数据同步功能,支持与外部MySQL数据库进行数据导入、导出和双向同步。此功能适用于需要与现有MySQL系统集成或进行数据备份的场景。

## 功能特性

### 1. 连接配置
- 支持配置MySQL数据库连接信息
- 包括主机地址、端口、数据库名、用户名、密码
- 提供连接测试功能,验证配置是否正确
- 支持启用/禁用同步功能

### 2. 数据导出
- 将Supabase中的所有数据导出到MySQL数据库
- 支持的数据表:
  - 产品信息(products)
  - 客户信息(customers)
  - 供应商信息(suppliers)
  - 仓库信息(warehouses)
  - 货架信息(shelves)
  - 字典数据(dictionaries)
  - 入库单(inbound_orders)及明细
  - 出库单(outbound_orders)及明细
  - 调拨单(transfer_orders)及明细
  - 序列号(serial_numbers)

### 3. 数据导入
- 从MySQL数据库导入数据到Supabase
- 支持批量导入所有表数据
- 自动处理数据格式转换

### 4. 全量同步
- 双向同步所有数据
- 比较两个数据库的数据差异
- 以最新的数据为准进行同步
- 保持两个数据库数据一致

### 5. 同步日志
- 记录所有同步操作
- 包括操作类型、状态、时间、详情
- 支持查看历史记录
- 便于问题排查和审计

## 配置步骤

### 第一步: 准备MySQL数据库

1. 确保MySQL服务器正在运行
2. 创建专用数据库:
   ```sql
   CREATE DATABASE inventory_system CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
   ```

3. 创建专用用户并授权:
   ```sql
   CREATE USER 'sync_user'@'%' IDENTIFIED BY '强密码';
   GRANT ALL PRIVILEGES ON inventory_system.* TO 'sync_user'@'%';
   FLUSH PRIVILEGES;
   ```

4. 确保MySQL允许远程连接(如需要):
   - 编辑MySQL配置文件(my.cnf或my.ini)
   - 设置 `bind-address = 0.0.0.0`
   - 重启MySQL服务

### 第二步: 配置系统连接

1. 登录系统(使用管理员账号)
2. 进入 **系统管理** → **MySQL同步**
3. 切换到 **配置管理** 标签页
4. 填写MySQL连接信息:
   - **主机地址**: MySQL服务器IP或域名(本地使用localhost)
   - **端口**: 默认3306
   - **数据库名**: 上面创建的数据库名
   - **用户名**: 上面创建的用户名
   - **密码**: 用户密码
5. 勾选 **启用MySQL同步**
6. 点击 **保存配置**
7. 点击 **测试连接** 验证配置

### 第三步: 执行同步操作

1. 切换到 **数据同步** 标签页
2. 根据需要选择操作:
   - **导出到MySQL**: 首次同步或需要覆盖MySQL数据时使用
   - **从MySQL导入**: 从MySQL恢复数据时使用
   - **全量同步**: 定期同步保持数据一致时使用
3. 点击对应的 **开始** 按钮
4. 等待同步完成

### 第四步: 查看同步日志

1. 切换到 **同步日志** 标签页
2. 查看同步历史记录
3. 点击 **查看详情** 查看具体信息
4. 如有失败记录,查看错误信息进行排查

## 使用场景

### 场景1: 数据备份
**需求**: 定期将Supabase数据备份到本地MySQL

**步骤**:
1. 配置本地MySQL连接
2. 定期执行 **导出到MySQL** 操作
3. 查看同步日志确认成功

### 场景2: 数据迁移
**需求**: 从现有MySQL系统迁移到本系统

**步骤**:
1. 确保MySQL数据表结构与系统一致
2. 配置MySQL连接
3. 执行 **从MySQL导入** 操作
4. 验证数据完整性

### 场景3: 双系统运行
**需求**: 同时使用本系统和其他MySQL系统

**步骤**:
1. 配置MySQL连接
2. 定期执行 **全量同步** 操作
3. 保持两个系统数据一致

## 重要提示

### ⚠️ 数据安全
- **导出操作会覆盖MySQL中的现有数据**,请谨慎操作
- **导入操作会覆盖Supabase中的现有数据**,建议先备份
- 首次使用建议先在测试环境验证

### ⚠️ 网络要求
- 确保服务器可以访问MySQL数据库
- 如果MySQL在内网,需要配置网络访问权限
- 建议使用VPN或专线连接,确保数据传输安全

### ⚠️ 性能考虑
- 大量数据同步可能需要较长时间
- 建议在业务低峰期执行同步操作
- 避免频繁执行全量同步

### ⚠️ 权限管理
- 只有管理员可以配置和执行同步操作
- 建议使用专用MySQL账号,限制权限范围
- 定期更换MySQL密码

## 技术说明

### 接口预留
当前Edge Function已预留MySQL连接接口,但由于Deno Deploy环境限制,不支持直接连接MySQL。

**实际使用需要以下方案之一**:

#### 方案1: 自托管环境部署
将Edge Function部署到支持MySQL的自托管环境:
- 使用Deno自托管
- 使用Docker容器
- 使用云服务器

#### 方案2: 中间API服务器
创建中间API服务器处理MySQL连接:
```
浏览器 → Supabase Edge Function → 中间API服务器 → MySQL
```

中间API服务器可以使用:
- Node.js + Express
- Python + Flask
- Go + Gin
- 任何支持MySQL的后端框架

#### 方案3: MySQL REST API
使用MySQL REST API或HTTP接口:
- MySQL Router REST API
- 自建REST API服务
- 第三方MySQL HTTP接口

### 数据表结构
MySQL数据库需要与Supabase保持相同的表结构。建议使用以下SQL创建表:

```sql
-- 产品表
CREATE TABLE products (
  id CHAR(36) PRIMARY KEY,
  code VARCHAR(50) UNIQUE NOT NULL,
  name VARCHAR(200) NOT NULL,
  specifications TEXT,
  unit VARCHAR(20),
  manufacturer VARCHAR(200),
  license_number VARCHAR(100),
  registration_number VARCHAR(100),
  default_shelf_id CHAR(36),
  default_style VARCHAR(20),
  storage_conditions TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- 其他表结构请参考Supabase数据库
```

## 常见问题

### Q: 连接测试失败怎么办?
A: 检查以下几点:
1. MySQL服务是否正常运行
2. 主机地址、端口、用户名、密码是否正确
3. MySQL用户是否有足够权限
4. 防火墙是否允许连接
5. 网络是否可达

### Q: 同步失败怎么办?
A: 
1. 查看同步日志中的错误信息
2. 检查MySQL表结构是否与系统一致
3. 检查数据格式是否正确
4. 确认MySQL有足够的存储空间

### Q: 如何验证同步是否成功?
A:
1. 查看同步日志,确认状态为"成功"
2. 在MySQL中查询数据,验证数据完整性
3. 比对关键数据的数量和内容

### Q: 可以只同步部分表吗?
A: 当前版本同步所有表,如需部分同步,请联系技术支持定制开发

### Q: 同步会影响系统使用吗?
A: 同步过程中系统可以正常使用,但建议在业务低峰期执行大量数据同步

## 技术支持

如遇到问题或需要定制开发,请联系系统管理员或技术支持团队。

---

**最后更新**: 2026-01-13
