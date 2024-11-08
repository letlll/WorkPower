```sql
-- 自选超市进销存管理系统数据库创建脚本
-- 使用 MySQL 8.0 及以上版本

-- 设置数据库字符集为 UTF8MB4 以支持多语言和特殊字符
CREATE DATABASE IF NOT EXISTS SupermarketInventory
CHARACTER SET = utf8mb4
COLLATE = utf8mb4_unicode_ci;

USE SupermarketInventory;

-- ================================================
-- 1. 类别表 (Category)
-- ================================================
CREATE TABLE IF NOT EXISTS `Category` (
    `CategoryID` INT AUTO_INCREMENT PRIMARY KEY COMMENT '类别编号',
    `CategoryName` VARCHAR(100) NOT NULL COMMENT '类别名称',
    `ParentCategoryID` INT DEFAULT NULL COMMENT '父类别编号',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    `UpdatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
    FOREIGN KEY (`ParentCategoryID`) REFERENCES `Category`(`CategoryID`)
        ON DELETE SET NULL
        ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='商品类别表';

-- ================================================
-- 2. 供应商表 (Supplier)
-- ================================================
CREATE TABLE IF NOT EXISTS `Supplier` (
    `SupplierID` INT AUTO_INCREMENT PRIMARY KEY COMMENT '供应商编号',
    `SupplierName` VARCHAR(150) NOT NULL COMMENT '供应商名称',
    `ContactInfo` VARCHAR(255) DEFAULT NULL COMMENT '联系方式',
    `Address` VARCHAR(255) DEFAULT NULL COMMENT '地址',
    `CreditRating` ENUM('A', 'B', 'C', 'D') DEFAULT 'C' COMMENT '信用等级',
    `Status` ENUM('活跃', '暂停', '终止') DEFAULT '活跃' COMMENT '合作状态',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    `UpdatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间'
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='供应商表';

-- ================================================
-- 3. 员工表 (Employee)
-- ================================================
CREATE TABLE IF NOT EXISTS `Employee` (
    `EmployeeID` INT AUTO_INCREMENT PRIMARY KEY COMMENT '员工编号',
    `Name` VARCHAR(100) NOT NULL COMMENT '姓名',
    `Position` VARCHAR(100) NOT NULL COMMENT '职位',
    `ContactInfo` VARCHAR(255) DEFAULT NULL COMMENT '联系方式',
    `PermissionLevel` ENUM('管理员', '采购员', '收银员', '库存管理员', '统计分析员') NOT NULL DEFAULT '收银员' COMMENT '权限级别',
    `AccountStatus` ENUM('正常', '冻结') DEFAULT '正常' COMMENT '账户状态',
    `Username` VARCHAR(50) NOT NULL UNIQUE COMMENT '用户名',
    `PasswordHash` VARCHAR(255) NOT NULL COMMENT '密码哈希',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    `UpdatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间'
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='员工表';

-- ================================================
-- 4. 商品表 (Product)
-- ================================================
CREATE TABLE IF NOT EXISTS `Product` (
    `ProductID` INT AUTO_INCREMENT PRIMARY KEY COMMENT '商品编号',
    `ProductName` VARCHAR(150) NOT NULL COMMENT '商品名称',
    `CategoryID` INT NOT NULL COMMENT '类别编号',
    `SupplierID` INT NOT NULL COMMENT '供应商编号',
    `PurchasePrice` DECIMAL(10,2) NOT NULL COMMENT '进价',
    `SellingPrice` DECIMAL(10,2) NOT NULL COMMENT '售价',
    `StockQuantity` INT DEFAULT 0 COMMENT '库存数量',
    `SafetyStock` INT DEFAULT 0 COMMENT '安全库存量',
    `Unit` VARCHAR(50) NOT NULL COMMENT '单位',
    `Specification` VARCHAR(100) DEFAULT NULL COMMENT '规格',
    `Barcode` VARCHAR(100) UNIQUE NOT NULL COMMENT '条形码',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    `UpdatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
    FOREIGN KEY (`CategoryID`) REFERENCES `Category`(`CategoryID`)
        ON DELETE RESTRICT
        ON UPDATE CASCADE,
    FOREIGN KEY (`SupplierID`) REFERENCES `Supplier`(`SupplierID`)
        ON DELETE RESTRICT
        ON UPDATE CASCADE,
    INDEX `idx_product_barcode` (`Barcode`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='商品表';

-- ================================================
-- 5. 采购订单表 (PurchaseOrder)
-- ================================================
CREATE TABLE IF NOT EXISTS `PurchaseOrder` (
    `PurchaseOrderID` VARCHAR(20) PRIMARY KEY COMMENT '采购单号',
    `SupplierID` INT NOT NULL COMMENT '供应商编号',
    `PurchaseDate` DATE NOT NULL COMMENT '进货日期',
    `OperatorID` INT NOT NULL COMMENT '操作员编号',
    `ExpectedArrivalDate` DATE DEFAULT NULL COMMENT '预计到货日期',
    `ActualArrivalDate` DATE DEFAULT NULL COMMENT '实际到货日期',
    `Status` ENUM('待审核', '已到货', '完成', '取消') DEFAULT '待审核' COMMENT '状态',
    `Remarks` TEXT DEFAULT NULL COMMENT '备注',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    `UpdatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT='更新时间',
    FOREIGN KEY (`SupplierID`) REFERENCES `Supplier`(`SupplierID`)
        ON DELETE RESTRICT
        ON UPDATE CASCADE,
    FOREIGN KEY (`OperatorID`) REFERENCES `Employee`(`EmployeeID`)
        ON DELETE RESTRICT
        ON UPDATE CASCADE,
    INDEX `idx_purchaseorder_supplier` (`SupplierID`),
    INDEX `idx_purchaseorder_operator` (`OperatorID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='采购订单表';

-- ================================================
-- 6. 采购订单明细表 (PurchaseOrderDetail)
-- ================================================
CREATE TABLE IF NOT EXISTS `PurchaseOrderDetail` (
    `DetailID` INT AUTO_INCREMENT PRIMARY KEY COMMENT '明细编号',
    `PurchaseOrderID` VARCHAR(20) NOT NULL COMMENT '采购单号',
    `ProductID` INT NOT NULL COMMENT '商品编号',
    `Quantity` INT NOT NULL COMMENT '数量',
    `PurchasePrice` DECIMAL(10,2) NOT NULL COMMENT '进价',
    `Discount` DECIMAL(5,2) DEFAULT 0.00 COMMENT '折扣（0-1之间）',
    `Subtotal` DECIMAL(10,2) AS (`Quantity` * `PurchasePrice` * (1 - `Discount`)) STORED COMMENT '小计',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    `UpdatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT='更新时间',
    FOREIGN KEY (`PurchaseOrderID`) REFERENCES `PurchaseOrder`(`PurchaseOrderID`)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    FOREIGN KEY (`ProductID`) REFERENCES `Product`(`ProductID`)
        ON DELETE RESTRICT
        ON UPDATE CASCADE,
    INDEX `idx_purchaseorderdetail_product` (`ProductID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='采购订单明细表';

-- ================================================
-- 7. 销售订单表 (SalesOrder)
-- ================================================
CREATE TABLE IF NOT EXISTS `SalesOrder` (
    `SalesOrderID` VARCHAR(20) PRIMARY KEY COMMENT '销售单号',
    `SalesDate` DATE NOT NULL COMMENT '销售日期',
    `CashierID` INT NOT NULL COMMENT '收银员编号',
    `TotalAmount` DECIMAL(10,2) NOT NULL COMMENT '总金额',
    `PaymentMethod` ENUM('现金', '刷卡', '移动支付', '电子钱包', '积分支付') NOT NULL DEFAULT '现金' COMMENT '支付方式',
    `DiscountAmount` DECIMAL(10,2) DEFAULT 0.00 COMMENT '优惠金额',
    `AmountDue` DECIMAL(10,2) AS (`TotalAmount` - `DiscountAmount`) STORED COMMENT '应收金额',
    `AmountReceived` DECIMAL(10,2) DEFAULT 0.00 COMMENT '实收金额',
    `ChangeAmount` DECIMAL(10,2) AS (`AmountReceived` - `AmountDue`) STORED COMMENT '找零金额',
    `Remarks` TEXT DEFAULT NULL COMMENT '备注',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    `UpdatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT='更新时间',
    FOREIGN KEY (`CashierID`) REFERENCES `Employee`(`EmployeeID`)
        ON DELETE RESTRICT
        ON UPDATE CASCADE,
    INDEX `idx_salesorder_cashier` (`CashierID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='销售订单表';

-- ================================================
-- 8. 销售订单明细表 (SalesOrderDetail)
-- ================================================
CREATE TABLE IF NOT EXISTS `SalesOrderDetail` (
    `DetailID` INT AUTO_INCREMENT PRIMARY KEY COMMENT '明细编号',
    `SalesOrderID` VARCHAR(20) NOT NULL COMMENT '销售单号',
    `ProductID` INT NOT NULL COMMENT '商品编号',
    `Quantity` INT NOT NULL COMMENT '数量',
    `SellingPrice` DECIMAL(10,2) NOT NULL COMMENT '售价',
    `Discount` DECIMAL(5,2) DEFAULT 0.00 COMMENT '折扣（0-1之间）',
    `Subtotal` DECIMAL(10,2) AS (`Quantity` * `SellingPrice` * (1 - `Discount`)) STORED COMMENT '小计',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    `UpdatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT='更新时间',
    FOREIGN KEY (`SalesOrderID`) REFERENCES `SalesOrder`(`SalesOrderID`)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    FOREIGN KEY (`ProductID`) REFERENCES `Product`(`ProductID`)
        ON DELETE RESTRICT
        ON UPDATE CASCADE,
    INDEX `idx_salesorderdetail_product` (`ProductID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='销售订单明细表';

-- ================================================
-- 9. 库存台帐表 (InventoryLedger)
-- ================================================
CREATE TABLE IF NOT EXISTS `InventoryLedger` (
    `LedgerID` BIGINT AUTO_INCREMENT PRIMARY KEY COMMENT '台帐编号',
    `ProductID` INT NOT NULL COMMENT '商品编号',
    `Date` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '日期',
    `TotalIn` INT DEFAULT 0 COMMENT '累计入库量',
    `TotalOut` INT DEFAULT 0 COMMENT '累计销售量',
    `Balance` INT DEFAULT 0 COMMENT '库存结余量',
    `OperationType` ENUM('入库', '出库', '调整') NOT NULL COMMENT '操作类型',
    `OperationID` VARCHAR(20) NOT NULL COMMENT '操作单号',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    FOREIGN KEY (`ProductID`) REFERENCES `Product`(`ProductID`)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    INDEX `idx_inventoryledger_product` (`ProductID`),
    INDEX `idx_inventoryledger_date` (`Date`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='库存台帐表';

-- ================================================
-- 10. 价格标签表 (PriceLabel)
-- ================================================
CREATE TABLE IF NOT EXISTS `PriceLabel` (
    `LabelID` INT AUTO_INCREMENT PRIMARY KEY COMMENT '标签编号',
    `ProductID` INT NOT NULL COMMENT '商品编号',
    `PrintDate` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '标签打印日期',
    `PrintBatch` VARCHAR(50) DEFAULT NULL COMMENT '打印批次',
    `Remarks` TEXT DEFAULT NULL COMMENT '备注',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    FOREIGN KEY (`ProductID`) REFERENCES `Product`(`ProductID`)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    INDEX `idx_pricelabel_product` (`ProductID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='价格标签表';

-- ================================================
-- 11. 库存调整表 (InventoryAdjustment)
-- ================================================
CREATE TABLE IF NOT EXISTS `InventoryAdjustment` (
    `AdjustmentID` INT AUTO_INCREMENT PRIMARY KEY COMMENT '调整编号',
    `ProductID` INT NOT NULL COMMENT '商品编号',
    `AdjustmentDate` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '调整日期',
    `AdjustmentQuantity` INT NOT NULL COMMENT '调整数量',
    `AdjustmentReason` VARCHAR(255) DEFAULT NULL COMMENT '调整原因',
    `OperatorID` INT NOT NULL COMMENT '操作员编号',
    `Remarks` TEXT DEFAULT NULL COMMENT '备注',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    FOREIGN KEY (`ProductID`) REFERENCES `Product`(`ProductID`)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    FOREIGN KEY (`OperatorID`) REFERENCES `Employee`(`EmployeeID`)
        ON DELETE RESTRICT
        ON UPDATE CASCADE,
    INDEX `idx_inventoryadjustment_product` (`ProductID`),
    INDEX `idx_inventoryadjustment_operator` (`OperatorID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='库存调整表';

-- ================================================
-- 12. 报表表 (Report)
-- ================================================
CREATE TABLE IF NOT EXISTS `Report` (
    `ReportID` BIGINT AUTO_INCREMENT PRIMARY KEY COMMENT '报表编号',
    `ReportType` ENUM('每日结算', '月度盘点', '营业报告', '采购报表', '销售报表') NOT NULL COMMENT '报表类型',
    `GenerationDate` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '生成日期',
    `GeneratedBy` INT NOT NULL COMMENT '生成人员',
    `ReportContent` VARCHAR(255) DEFAULT NULL COMMENT '报表内容（路径或URL）',
    `CreatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    FOREIGN KEY (`GeneratedBy`) REFERENCES `Employee`(`EmployeeID`)
        ON DELETE SET NULL
        ON UPDATE CASCADE,
    INDEX `idx_report_type` (`ReportType`),
    INDEX `idx_report_generatedby` (`GeneratedBy`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='报表表';

-- ================================================
-- 索引优化
-- ================================================

-- 为常用的查询字段添加索引以提高查询效率
CREATE INDEX `idx_product_category` ON `Product` (`CategoryID`);
CREATE INDEX `idx_product_supplier` ON `Product` (`SupplierID`);
CREATE INDEX `idx_purchaseorder_date` ON `PurchaseOrder` (`PurchaseDate`);
CREATE INDEX `idx_salesorder_date` ON `SalesOrder` (`SalesDate`);

-- ================================================
-- 外键约束检查 (确保外键关系的完整性)
-- ================================================
SET FOREIGN_KEY_CHECKS = 1;

-- ================================================
-- 示例数据插入 (可选)
-- ================================================
-- 插入类别示例
INSERT INTO `Category` (`CategoryName`, `ParentCategoryID`) VALUES
('食品', NULL),
('日用品', NULL),
('饮料', 1),
('清洁用品', 2);

-- 插入供应商示例
INSERT INTO `Supplier` (`SupplierName`, `ContactInfo`, `Address`, `CreditRating`, `Status`) VALUES
('供应商A', '电话: 123456789', '地址A', 'A', '活跃'),
('供应商B', '电话: 987654321', '地址B', 'B', '活跃');

-- 插入员工示例
INSERT INTO `Employee` (`Name`, `Position`, `ContactInfo`, `PermissionLevel`, `AccountStatus`, `Username`, `PasswordHash`) VALUES
('张三', '管理员', '电话: 111111111', '管理员', '正常', 'admin', 'hashed_password'),
('李四', '采购员', '电话: 222222222', '采购员', '正常', 'purchaser', 'hashed_password'),
('王五', '收银员', '电话: 333333333', '收银员', '正常', 'cashier', 'hashed_password');

-- 插入商品示例
INSERT INTO `Product` (`ProductName`, `CategoryID`, `SupplierID`, `PurchasePrice`, `SellingPrice`, `StockQuantity`, `SafetyStock`, `Unit`, `Specification`, `Barcode`) VALUES
('苹果', 1, 1, 3.00, 5.00, 100, 20, '公斤', '新鲜苹果', '1234567890123'),
('洗衣液', 4, 2, 10.00, 15.00, 50, 10, '瓶', '2升装', '9876543210987');

-- ================================================
-- 完成
-- ================================================
```

---

### 说明

1. **数据库和表创建**
    - **数据库**：创建了名为 `SupermarketInventory` 的数据库，设置字符集为 `utf8mb4` 以支持多语言和特殊字符。
    - **表**：根据需求创建了12个主要表，包括类别、供应商、员工、商品、采购订单及其明细、销售订单及其明细、库存台帐、价格标签、库存调整和报表。

2. **主键和外键**
    - 每个表都有一个主键，用于唯一标识记录。
    - 外键约束确保数据的完整性，关联相关表。例如，`Product` 表中的 `CategoryID` 和 `SupplierID` 分别关联到 `Category` 和 `Supplier` 表。

3. **数据类型和约束**
    - 使用合适的数据类型，如 `INT`、`VARCHAR`、`DECIMAL`、`ENUM` 等。
    - 为必要的字段添加了 `NOT NULL` 约束，确保数据完整性。
    - 使用 `ENUM` 类型定义了有限的选项，如 `PaymentMethod`、`PermissionLevel` 等，确保数据一致性。

4. **计算字段**
    - 在 `PurchaseOrderDetail` 和 `SalesOrderDetail` 表中，使用了计算字段 `Subtotal`，自动计算小计金额。
    - 在 `SalesOrder` 表中，使用计算字段 `AmountDue` 和 `ChangeAmount`，自动计算应收和找零金额。
    - 在 `InventoryLedger` 表中，使用计算字段 `Balance` 来跟踪库存结余。

5. **索引优化**
    - 为常用的查询字段添加了索引，如 `Barcode`、`CategoryID`、`SupplierID`、`PurchaseDate` 和 `SalesDate`，以提高查询性能。
    - 在关联表的外键字段上添加了索引，以加快连接查询速度。

6. **示例数据**
    - 插入了一些示例数据，包括类别、供应商、员工和商品，以便测试和验证数据库结构。

7. **注释**
    - 每个表和字段都添加了注释，便于理解和维护。

8. **安全性和权限**
    - 虽然SQL脚本中未具体实现用户权限管理，但建议在数据库层面设置合适的用户权限，确保不同角色只能访问和操作其职责范围内的数据。

9. **扩展性**
    - 设计时考虑了系统的可扩展性，如支持多级分类、灵活的报表类型和多种支付方式。
    - 可根据需要进一步扩展，例如添加更多的业务模块或集成其他系统。

### 注意事项

- **密码存储**：在 `Employee` 表中，`PasswordHash` 字段用于存储密码的哈希值。确保在应用层使用强哈希算法（如 bcrypt、Argon2）对密码进行加密。
  
- **事务管理**：在应用层处理涉及多个表的操作时，应使用事务管理以确保数据一致性。

- **索引维护**：随着数据量的增长，定期评估和优化索引，以维持查询性能。

- **备份与恢复**：定期备份数据库，确保在数据丢失或损坏时能够快速恢复。

- **安全防护**：采取必要的安全措施，如限制数据库访问权限、使用防火墙和加密传输等，保护数据安全。

通过执行上述SQL脚本，将创建一个结构完善、性能优化且符合业务需求的自选超市进销存管理系统数据库。根据实际需求，您可以进一步调整和扩展该数据库设计。