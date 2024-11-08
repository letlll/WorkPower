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
    `UpdatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT='更新时间'
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
    `UpdatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT='更新时间'
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
    `UpdatedAt` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT='更新时间',
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
('王五', '收银员', '电话: 333333333', '收银员', '正常', 'cashier', 'hashed_password'),
('赵六', '库存管理员', '电话: 444444444', '库存管理员', '正常', 'inventory', 'hashed_password'),
('孙七', '统计分析员', '电话: 555555555', '统计分析员', '正常', 'analyst', 'hashed_password');

-- 插入商品示例
INSERT INTO `Product` (`ProductName`, `CategoryID`, `SupplierID`, `PurchasePrice`, `SellingPrice`, `StockQuantity`, `SafetyStock`, `Unit`, `Specification`, `Barcode`) VALUES
('苹果', 1, 1, 3.00, 5.00, 100, 20, '公斤', '新鲜苹果', '1234567890123'),
('洗衣液', 4, 2, 10.00, 15.00, 50, 10, '瓶', '2升装', '9876543210987'),
('矿泉水', 3, 1, 0.50, 1.00, 200, 50, '瓶', '500ml', '1111111111111'),
('牙膏', 2, 2, 2.00, 3.50, 150, 30, '支', '75g', '2222222222222');

-- 插入采购订单示例
INSERT INTO `PurchaseOrder` (`PurchaseOrderID`, `SupplierID`, `PurchaseDate`, `OperatorID`, `ExpectedArrivalDate`, `ActualArrivalDate`, `Status`, `Remarks`) VALUES
('PO20231001', 1, '2023-10-01', 2, '2023-10-05', '2023-10-04', '已到货', '无'),
('PO20231002', 2, '2023-10-02', 2, '2023-10-06', NULL, '待审核', '急单');

-- 插入采购订单明细示例
INSERT INTO `PurchaseOrderDetail` (`PurchaseOrderID`, `ProductID`, `Quantity`, `PurchasePrice`, `Discount`) VALUES
('PO20231001', 1, 50, 3.00, 0.05),
('PO20231001', 3, 100, 0.50, 0.00),
('PO20231002', 2, 30, 10.00, 0.10),
('PO20231002', 4, 40, 2.00, 0.00);

-- 插入销售订单示例
INSERT INTO `SalesOrder` (`SalesOrderID`, `SalesDate`, `CashierID`, `TotalAmount`, `PaymentMethod`, `DiscountAmount`, `AmountReceived`) VALUES
('SO20231001', '2023-10-03', 3, 50.00, '现金', 5.00, 45.00),
('SO20231002', '2023-10-04', 3, 30.00, '刷卡', 0.00, 30.00);

-- 插入销售订单明细示例
INSERT INTO `SalesOrderDetail` (`SalesOrderID`, `ProductID`, `Quantity`, `SellingPrice`, `Discount`) VALUES
('SO20231001', 1, 5, 5.00, 0.10),
('SO20231001', 3, 10, 1.00, 0.00),
('SO20231002', 2, 2, 15.00, 0.00),
('SO20231002', 4, 3, 3.50, 0.05);

-- 插入库存台帐示例
INSERT INTO `InventoryLedger` (`ProductID`, `Date`, `TotalIn`, `TotalOut`, `Balance`, `OperationType`, `OperationID`) VALUES
(1, '2023-10-01', 50, 0, 150, '入库', 'PO20231001'),
(3, '2023-10-01', 100, 0, 300, '入库', 'PO20231001'),
(2, '2023-10-02', 30, 0, 80, '入库', 'PO20231002'),
(4, '2023-10-02', 40, 0, 190, '入库', 'PO20231002'),
(1, '2023-10-03', 0, 5, 145, '出库', 'SO20231001'),
(3, '2023-10-03', 0, 10, 290, '出库', 'SO20231001'),
(2, '2023-10-04', 0, 2, 78, '出库', 'SO20231002'),
(4, '2023-10-04', 0, 3, 187, '出库', 'SO20231002');

-- 插入价格标签示例
INSERT INTO `PriceLabel` (`ProductID`, `PrintBatch`) VALUES
(1, 'Batch001'),
(2, 'Batch001'),
(3, 'Batch002'),
(4, 'Batch002');

-- 插入库存调整示例
INSERT INTO `InventoryAdjustment` (`ProductID`, `AdjustmentQuantity`, `AdjustmentReason`, `OperatorID`) VALUES
(1, -5, '损坏', 4),
(2, 10, '盘点补充', 4);

-- 插入报表示例
INSERT INTO `Report` (`ReportType`, `GeneratedBy`, `ReportContent`) VALUES
('每日结算', 5, '/reports/daily_settlement_20231003.pdf'),
('月度盘点', 5, '/reports/monthly_inventory_202310.pdf');

-- ================================================
-- 数据操作 (Data Operations) - 10条
-- ================================================

-- 1. 更新商品售价
UPDATE `Product`
SET `SellingPrice` = `SellingPrice` * 1.10
WHERE `ProductID` = 1;

-- 2. 删除停用供应商
DELETE FROM `Supplier`
WHERE `Status` = '终止';

-- 3. 插入新的采购订单
INSERT INTO `PurchaseOrder` (`PurchaseOrderID`, `SupplierID`, `PurchaseDate`, `OperatorID`, `ExpectedArrivalDate`, `Status`, `Remarks`)
VALUES ('PO20231003', 1, '2023-10-05', 2, '2023-10-09', '待审核', '补充库存');

-- 4. 插入采购订单明细
INSERT INTO `PurchaseOrderDetail` (`PurchaseOrderID`, `ProductID`, `Quantity`, `PurchasePrice`, `Discount`)
VALUES ('PO20231003', 1, 20, 3.00, 0.05),
       ('PO20231003', 3, 50, 0.50, 0.00);

-- 5. 更新库存数量
UPDATE `Product`
SET `StockQuantity` = `StockQuantity` + 20
WHERE `ProductID` = 1;

-- 6. 插入新的销售订单
INSERT INTO `SalesOrder` (`SalesOrderID`, `SalesDate`, `CashierID`, `TotalAmount`, `PaymentMethod`, `DiscountAmount`, `AmountReceived`)
VALUES ('SO20231003', '2023-10-05', 3, 100.00, '移动支付', 10.00, 90.00);

-- 7. 插入销售订单明细
INSERT INTO `SalesOrderDetail` (`SalesOrderID`, `ProductID`, `Quantity`, `SellingPrice`, `Discount`)
VALUES ('SO20231003', 1, 10, 5.50, 0.10),
       ('SO20231003', 2, 5, 15.00, 0.00);

-- 8. 调整库存数量
UPDATE `Product`
SET `StockQuantity` = `StockQuantity` - 10
WHERE `ProductID` = 2;

-- 9. 插入库存台帐记录
INSERT INTO `InventoryLedger` (`ProductID`, `TotalOut`, `Balance`, `OperationType`, `OperationID`)
VALUES (2, 5, 73, '出库', 'SO20231003');

-- 10. 更新报表内容
UPDATE `Report`
SET `ReportContent` = '/reports/daily_settlement_20231005.pdf'
WHERE `ReportType` = '每日结算' AND `ReportID` = 1;

-- ================================================
-- 单表查询 (Single Table Queries) - 15条
-- ================================================

-- 1. 查询所有商品
SELECT * FROM `Product`;

-- 2. 查询所有活跃供应商
SELECT * FROM `Supplier` WHERE `Status` = '活跃';

-- 3. 查询库存低于安全库存的商品
SELECT `ProductID`, `ProductName`, `StockQuantity`, `SafetyStock`
FROM `Product`
WHERE `StockQuantity` < `SafetyStock`;

-- 4. 查询所有管理员
SELECT `EmployeeID`, `Name`, `Username`
FROM `Employee`
WHERE `PermissionLevel` = '管理员';

-- 5. 查询特定类别的商品
SELECT `ProductID`, `ProductName`, `SellingPrice`
FROM `Product`
WHERE `CategoryID` = 1;

-- 6. 查询最近7天的采购订单
SELECT * FROM `PurchaseOrder`
WHERE `PurchaseDate` >= CURDATE() - INTERVAL 7 DAY;

-- 7. 查询某供应商的所有采购订单
SELECT `PurchaseOrderID`, `PurchaseDate`, `Status`
FROM `PurchaseOrder`
WHERE `SupplierID` = 1;

-- 8. 查询所有销售订单的总金额
SELECT `SalesOrderID`, `TotalAmount`
FROM `SalesOrder`;

-- 9. 查询某收银员的销售记录
SELECT `SalesOrderID`, `SalesDate`, `TotalAmount`
FROM `SalesOrder`
WHERE `CashierID` = 3;

-- 10. 查询库存调整记录
SELECT `AdjustmentID`, `ProductID`, `AdjustmentQuantity`, `AdjustmentReason`, `AdjustmentDate`
FROM `InventoryAdjustment`;

-- 11. 查询所有报表类型
SELECT DISTINCT `ReportType` FROM `Report`;

-- 12. 查询价格标签信息
SELECT `LabelID`, `ProductID`, `PrintDate`, `PrintBatch`
FROM `PriceLabel`;

-- 13. 查询员工账户状态
SELECT `EmployeeID`, `Name`, `AccountStatus`
FROM `Employee`;

-- 14. 查询某商品的采购历史
SELECT `PurchaseOrderID`, `PurchaseDate`, `Quantity`, `PurchasePrice`, `Discount`
FROM `PurchaseOrderDetail`
WHERE `ProductID` = 1;

-- 15. 查询某商品的销售历史
SELECT `SalesOrderID`, `SalesDate`, `Quantity`, `SellingPrice`, `Discount`
FROM `SalesOrderDetail`
WHERE `ProductID` = 1;

-- ================================================
-- 多表查询 (Multi-Table Queries) - 15条
-- ================================================

-- 16. 查询每个商品的类别名称
SELECT p.`ProductID`, p.`ProductName`, c.`CategoryName`
FROM `Product` p
JOIN `Category` c ON p.`CategoryID` = c.`CategoryID`;

-- 17. 查询采购订单及其供应商信息
SELECT po.`PurchaseOrderID`, po.`PurchaseDate`, s.`SupplierName`, po.`Status`
FROM `PurchaseOrder` po
JOIN `Supplier` s ON po.`SupplierID` = s.`SupplierID`;

-- 18. 查询销售订单及其收银员信息
SELECT so.`SalesOrderID`, so.`SalesDate`, e.`Name` AS `CashierName`, so.`TotalAmount`
FROM `SalesOrder` so
JOIN `Employee` e ON so.`CashierID` = e.`EmployeeID`;

-- 19. 查询采购订单明细及商品信息
SELECT pod.`DetailID`, pod.`PurchaseOrderID`, p.`ProductName`, pod.`Quantity`, pod.`Subtotal`
FROM `PurchaseOrderDetail` pod
JOIN `Product` p ON pod.`ProductID` = p.`ProductID`;

-- 20. 查询销售订单明细及商品信息
SELECT sod.`DetailID`, sod.`SalesOrderID`, p.`ProductName`, sod.`Quantity`, sod.`Subtotal`
FROM `SalesOrderDetail` sod
JOIN `Product` p ON sod.`ProductID` = p.`ProductID`;

-- 21. 查询库存台帐及商品信息
SELECT il.`LedgerID`, il.`Date`, p.`ProductName`, il.`OperationType`, il.`TotalIn`, il.`TotalOut`, il.`Balance`
FROM `InventoryLedger` il
JOIN `Product` p ON il.`ProductID` = p.`ProductID`;

-- 22. 查询库存调整记录及操作员信息
SELECT ia.`AdjustmentID`, ia.`AdjustmentDate`, p.`ProductName`, ia.`AdjustmentQuantity`, e.`Name` AS `OperatorName`
FROM `InventoryAdjustment` ia
JOIN `Product` p ON ia.`ProductID` = p.`ProductID`
JOIN `Employee` e ON ia.`OperatorID` = e.`EmployeeID`;

-- 23. 查询报表生成信息及生成人员
SELECT r.`ReportID`, r.`ReportType`, r.`GenerationDate`, e.`Name` AS `GeneratedBy`, r.`ReportContent`
FROM `Report` r
JOIN `Employee` e ON r.`GeneratedBy` = e.`EmployeeID`;

-- 24. 查询每个供应商的商品供应情况
SELECT s.`SupplierName`, p.`ProductName`, p.`StockQuantity`
FROM `Supplier` s
JOIN `Product` p ON s.`SupplierID` = p.`SupplierID`;

-- 25. 查询每个类别的总库存
SELECT c.`CategoryName`, SUM(p.`StockQuantity`) AS `TotalStock`
FROM `Category` c
JOIN `Product` p ON c.`CategoryID` = p.`CategoryID`
GROUP BY c.`CategoryName`;

-- 26. 查询每日销售总额
SELECT so.`SalesDate`, SUM(so.`TotalAmount`) AS `DailySalesTotal`
FROM `SalesOrder` so
GROUP BY so.`SalesDate`;

-- 27. 查询每位收银员的销售总额
SELECT e.`Name` AS `CashierName`, SUM(so.`TotalAmount`) AS `TotalSales`
FROM `SalesOrder` so
JOIN `Employee` e ON so.`CashierID` = e.`EmployeeID`
GROUP BY e.`Name`;

-- 28. 查询每个供应商的采购总额
SELECT s.`SupplierName`, SUM(pod.`Subtotal`) AS `TotalPurchases`
FROM `PurchaseOrderDetail` pod
JOIN `PurchaseOrder` po ON pod.`PurchaseOrderID` = po.`PurchaseOrderID`
JOIN `Supplier` s ON po.`SupplierID` = s.`SupplierID`
GROUP BY s.`SupplierName`;

-- 29. 查询每个商品的月度销售量
SELECT p.`ProductName`, MONTH(so.`SalesDate`) AS `Month`, SUM(sod.`Quantity`) AS `MonthlySales`
FROM `SalesOrderDetail` sod
JOIN `SalesOrder` so ON sod.`SalesOrderID` = so.`SalesOrderID`
JOIN `Product` p ON sod.`ProductID` = p.`ProductID`
GROUP BY p.`ProductName`, MONTH(so.`SalesDate`);

-- 30. 查询每个员工的操作记录
SELECT e.`Name`, ia.`AdjustmentID`, ia.`AdjustmentQuantity`, ia.`AdjustmentReason`, ia.`AdjustmentDate`
FROM `InventoryAdjustment` ia
JOIN `Employee` e ON ia.`OperatorID` = e.`EmployeeID`;

-- ================================================
-- 存储过程与视图 (Stored Procedures and Views) - 10条
-- ================================================

-- 31. 创建存储过程：添加新商品
DELIMITER //
CREATE PROCEDURE `AddNewProduct` (
    IN p_ProductName VARCHAR(150),
    IN p_CategoryID INT,
    IN p_SupplierID INT,
    IN p_PurchasePrice DECIMAL(10,2),
    IN p_SellingPrice DECIMAL(10,2),
    IN p_StockQuantity INT,
    IN p_SafetyStock INT,
    IN p_Unit VARCHAR(50),
    IN p_Specification VARCHAR(100),
    IN p_Barcode VARCHAR(100)
)
BEGIN
    INSERT INTO `Product` (
        `ProductName`, `CategoryID`, `SupplierID`, `PurchasePrice`, `SellingPrice`,
        `StockQuantity`, `SafetyStock`, `Unit`, `Specification`, `Barcode`
    ) VALUES (
        p_ProductName, p_CategoryID, p_SupplierID, p_PurchasePrice, p_SellingPrice,
        p_StockQuantity, p_SafetyStock, p_Unit, p_Specification, p_Barcode
    );
END //
DELIMITER ;

-- 32. 创建存储过程：更新库存
DELIMITER //
CREATE PROCEDURE `UpdateStock` (
    IN p_ProductID INT,
    IN p_Quantity INT,
    IN p_OperationType ENUM('入库', '出库', '调整'),
    IN p_OperationID VARCHAR(20)
)
BEGIN
    DECLARE current_balance INT;

    SELECT `StockQuantity` INTO current_balance FROM `Product` WHERE `ProductID` = p_ProductID;

    IF p_OperationType = '入库' THEN
        UPDATE `Product`
        SET `StockQuantity` = `StockQuantity` + p_Quantity
        WHERE `ProductID` = p_ProductID;
        INSERT INTO `InventoryLedger` (`ProductID`, `TotalIn`, `Balance`, `OperationType`, `OperationID`)
        VALUES (p_ProductID, p_Quantity, current_balance + p_Quantity, p_OperationType, p_OperationID);
    ELSEIF p_OperationType = '出库' THEN
        UPDATE `Product`
        SET `StockQuantity` = `StockQuantity` - p_Quantity
        WHERE `ProductID` = p_ProductID;
        INSERT INTO `InventoryLedger` (`ProductID`, `TotalOut`, `Balance`, `OperationType`, `OperationID`)
        VALUES (p_ProductID, p_Quantity, current_balance - p_Quantity, p_OperationType, p_OperationID);
    ELSEIF p_OperationType = '调整' THEN
        UPDATE `Product`
        SET `StockQuantity` = `StockQuantity` + p_Quantity
        WHERE `ProductID` = p_ProductID;
        INSERT INTO `InventoryLedger` (`ProductID`, `Balance`, `OperationType`, `OperationID`)
        VALUES (p_ProductID, current_balance + p_Quantity, p_OperationType, p_OperationID);
    END IF;
END //
DELIMITER ;

-- 33. 创建视图：当前库存视图
CREATE VIEW `CurrentInventory` AS
SELECT p.`ProductID`, p.`ProductName`, p.`StockQuantity`, p.`SafetyStock`
FROM `Product` p;

-- 34. 创建视图：销售汇总视图
CREATE VIEW `SalesSummary` AS
SELECT sod.`ProductID`, p.`ProductName`, SUM(sod.`Quantity`) AS `TotalSold`, SUM(sod.`Subtotal`) AS `TotalRevenue`
FROM `SalesOrderDetail` sod
JOIN `Product` p ON sod.`ProductID` = p.`ProductID`
GROUP BY sod.`ProductID`, p.`ProductName`;

-- 35. 创建存储过程：生成每日销售报告
DELIMITER //
CREATE PROCEDURE `GenerateDailySalesReport` (
    IN p_Date DATE
)
BEGIN
    INSERT INTO `Report` (`ReportType`, `GenerationDate`, `GeneratedBy`, `ReportContent`)
    VALUES (
        '每日结算',
        NOW(),
        5, -- 假设统计分析员的EmployeeID为5
        CONCAT('/reports/daily_sales_', p_Date, '.pdf')
    );
END //
DELIMITER ;

-- 36. 创建存储过程：获取低库存商品
DELIMITER //
CREATE PROCEDURE `GetLowStockProducts` ()
BEGIN
    SELECT `ProductID`, `ProductName`, `StockQuantity`, `SafetyStock`
    FROM `Product`
    WHERE `StockQuantity` < `SafetyStock`;
END //
DELIMITER ;

-- 37. 创建视图：采购订单详情视图
CREATE VIEW `PurchaseOrderDetailsView` AS
SELECT po.`PurchaseOrderID`, s.`SupplierName`, po.`PurchaseDate`, e.`Name` AS `OperatorName`,
       pod.`ProductID`, p.`ProductName`, pod.`Quantity`, pod.`PurchasePrice`, pod.`Discount`, pod.`Subtotal`
FROM `PurchaseOrder` po
JOIN `Supplier` s ON po.`SupplierID` = s.`SupplierID`
JOIN `Employee` e ON po.`OperatorID` = e.`EmployeeID`
JOIN `PurchaseOrderDetail` pod ON po.`PurchaseOrderID` = pod.`PurchaseOrderID`
JOIN `Product` p ON pod.`ProductID` = p.`ProductID`;

-- 38. 创建视图：销售订单详情视图
CREATE VIEW `SalesOrderDetailsView` AS
SELECT so.`SalesOrderID`, so.`SalesDate`, e.`Name` AS `CashierName`, so.`TotalAmount`, so.`PaymentMethod`,
       sod.`ProductID`, p.`ProductName`, sod.`Quantity`, sod.`SellingPrice`, sod.`Discount`, sod.`Subtotal`
FROM `SalesOrder` so
JOIN `Employee` e ON so.`CashierID` = e.`EmployeeID`
JOIN `SalesOrderDetail` sod ON so.`SalesOrderID` = sod.`SalesOrderID`
JOIN `Product` p ON sod.`ProductID` = p.`ProductID`;

-- 39. 创建存储过程：计算月度库存结余
DELIMITER //
CREATE PROCEDURE `CalculateMonthlyInventory` (
    IN p_Month INT,
    IN p_Year INT
)
BEGIN
    INSERT INTO `Report` (`ReportType`, `GenerationDate`, `GeneratedBy`, `ReportContent`)
    SELECT '月度盘点', NOW(), 5, CONCAT('/reports/monthly_inventory_', p_Year, '_', p_Month, '.pdf')
    FROM DUAL;
END //
DELIMITER ;

-- 40. 创建视图：员工权限视图
CREATE VIEW `EmployeePermissions` AS
SELECT `EmployeeID`, `Name`, `PermissionLevel`
FROM `Employee`;

-- 41. 创建存储过程：获取销售订单总额
DELIMITER //
CREATE PROCEDURE `GetTotalSales` (
    IN p_StartDate DATE,
    IN p_EndDate DATE,
    OUT p_Total DECIMAL(10,2)
)
BEGIN
    SELECT SUM(`TotalAmount`) INTO p_Total
    FROM `SalesOrder`
    WHERE `SalesDate` BETWEEN p_StartDate AND p_EndDate;
END //
DELIMITER ;

-- 42. 创建视图：库存调整记录视图
CREATE VIEW `InventoryAdjustmentsView` AS
SELECT ia.`AdjustmentID`, ia.`ProductID`, p.`ProductName`, ia.`AdjustmentQuantity`,
       ia.`AdjustmentReason`, e.`Name` AS `OperatorName`, ia.`AdjustmentDate`
FROM `InventoryAdjustment` ia
JOIN `Product` p ON ia.`ProductID` = p.`ProductID`
JOIN `Employee` e ON ia.`OperatorID` = e.`EmployeeID`;

-- 43. 创建存储过程：获取供应商绩效
DELIMITER //
CREATE PROCEDURE `GetSupplierPerformance` (
    IN p_SupplierID INT
)
BEGIN
    SELECT s.`SupplierName`, COUNT(po.`PurchaseOrderID`) AS `TotalOrders`,
           SUM(pod.`Subtotal`) AS `TotalAmount`
    FROM `PurchaseOrder` po
    JOIN `PurchaseOrderDetail` pod ON po.`PurchaseOrderID` = pod.`PurchaseOrderID`
    JOIN `Supplier` s ON po.`SupplierID` = s.`SupplierID`
    WHERE s.`SupplierID` = p_SupplierID
    GROUP BY s.`SupplierName`;
END //
DELIMITER ;

-- 44. 创建视图：月度销售汇总视图
CREATE VIEW `MonthlySalesSummary` AS
SELECT MONTH(so.`SalesDate`) AS `Month`, YEAR(so.`SalesDate`) AS `Year`,
       SUM(so.`TotalAmount`) AS `MonthlySales`
FROM `SalesOrder` so
GROUP BY YEAR(so.`SalesDate`), MONTH(so.`SalesDate`);

-- 45. 创建存储过程：添加库存调整记录
DELIMITER //
CREATE PROCEDURE `AddInventoryAdjustment` (
    IN p_ProductID INT,
    IN p_AdjustmentQuantity INT,
    IN p_AdjustmentReason VARCHAR(255),
    IN p_OperatorID INT
)
BEGIN
    INSERT INTO `InventoryAdjustment` (`ProductID`, `AdjustmentQuantity`, `AdjustmentReason`, `OperatorID`)
    VALUES (p_ProductID, p_AdjustmentQuantity, p_AdjustmentReason, p_OperatorID);
END //
DELIMITER ;

-- 46. 创建视图：供应商商品供应视图
CREATE VIEW `SupplierProductSupply` AS
SELECT s.`SupplierName`, p.`ProductID`, p.`ProductName`, p.`StockQuantity`
FROM `Supplier` s
JOIN `Product` p ON s.`SupplierID` = p.`SupplierID`;

-- 47. 创建存储过程：获取库存报警商品
DELIMITER //
CREATE PROCEDURE `GetStockAlerts` ()
BEGIN
    SELECT `ProductID`, `ProductName`, `StockQuantity`, `SafetyStock`
    FROM `Product`
    WHERE `StockQuantity` < `SafetyStock`;
END //
DELIMITER ;

-- 48. 创建视图：财务报表视图
CREATE VIEW `FinancialReports` AS
SELECT r.`ReportID`, r.`ReportType`, r.`GenerationDate`, e.`Name` AS `GeneratedBy`,
       r.`ReportContent`
FROM `Report` r
JOIN `Employee` e ON r.`GeneratedBy` = e.`EmployeeID`
WHERE r.`ReportType` IN ('每日结算', '月度盘点');

-- 49. 创建存储过程：生成销售订单
DELIMITER //
CREATE PROCEDURE `CreateSalesOrder` (
    IN p_SalesOrderID VARCHAR(20),
    IN p_SalesDate DATE,
    IN p_CashierID INT,
    IN p_PaymentMethod ENUM('现金', '刷卡', '移动支付', '电子钱包', '积分支付'),
    IN p_DiscountAmount DECIMAL(10,2),
    IN p_AmountReceived DECIMAL(10,2),
    IN p_Remarks TEXT
)
BEGIN
    INSERT INTO `SalesOrder` (
        `SalesOrderID`, `SalesDate`, `CashierID`, `TotalAmount`,
        `PaymentMethod`, `DiscountAmount`, `AmountReceived`, `Remarks`
    ) VALUES (
        p_SalesOrderID, p_SalesDate, p_CashierID, 0.00,
        p_PaymentMethod, p_DiscountAmount, p_AmountReceived, p_Remarks
    );
END //
DELIMITER ;

-- 50. 创建视图：员工工作记录视图
CREATE VIEW `EmployeeWorkRecords` AS
SELECT e.`EmployeeID`, e.`Name`, e.`Position`,
       COUNT(po.`PurchaseOrderID`) AS `TotalPurchaseOrders`,
       COUNT(so.`SalesOrderID`) AS `TotalSalesOrders`,
       COUNT(ia.`AdjustmentID`) AS `TotalAdjustments`,
       COUNT(r.`ReportID`) AS `TotalReports`
FROM `Employee` e
LEFT JOIN `PurchaseOrder` po ON e.`EmployeeID` = po.`OperatorID`
LEFT JOIN `SalesOrder` so ON e.`EmployeeID` = so.`CashierID`
LEFT JOIN `InventoryAdjustment` ia ON e.`EmployeeID` = ia.`OperatorID`
LEFT JOIN `Report` r ON e.`EmployeeID` = r.`GeneratedBy`
GROUP BY e.`EmployeeID`, e.`Name`, e.`Position`;

-- ================================================
-- 更多数据操作 (Additional Data Operations) - 10条
-- ================================================

-- 51. 更新员工账户状态为冻结
UPDATE `Employee`
SET `AccountStatus` = '冻结'
WHERE `EmployeeID` = 3;

-- 52. 插入新的价格标签
INSERT INTO `PriceLabel` (`ProductID`, `PrintBatch`, `Remarks`) VALUES
(1, 'Batch003', '新季节标签'),
(2, 'Batch003', '促销标签');

-- 53. 删除库存调整记录
DELETE FROM `InventoryAdjustment`
WHERE `AdjustmentID` = 1;

-- 54. 更新采购订单状态为完成
UPDATE `PurchaseOrder`
SET `Status` = '完成', `ActualArrivalDate` = '2023-10-10'
WHERE `PurchaseOrderID` = 'PO20231003';

-- 55. 插入新的库存台帐记录
INSERT INTO `InventoryLedger` (`ProductID`, `TotalIn`, `Balance`, `OperationType`, `OperationID`)
VALUES (1, 20, 165, '入库', 'PO20231003'),
       (3, 50, 340, '入库', 'PO20231003');

-- 56. 更新销售订单总金额
UPDATE `SalesOrder`
SET `TotalAmount` = 120.00, `AmountReceived` = 110.00
WHERE `SalesOrderID` = 'SO20231003';

-- 57. 插入新的销售订单明细
INSERT INTO `SalesOrderDetail` (`SalesOrderID`, `ProductID`, `Quantity`, `SellingPrice`, `Discount`)
VALUES ('SO20231003', 3, 20, 1.00, 0.00),
       ('SO20231003', 4, 5, 3.50, 0.00);

-- 58. 删除已取消的采购订单
DELETE FROM `PurchaseOrder`
WHERE `PurchaseOrderID` = 'PO20231002';

-- 59. 更新商品库存数量
UPDATE `Product`
SET `StockQuantity` = `StockQuantity` + 10
WHERE `ProductID` = 4;

-- 60. 插入新的报表记录
INSERT INTO `Report` (`ReportType`, `GeneratedBy`, `ReportContent`)
VALUES ('营业报告', 5, '/reports/business_report_20231005.pdf');

-- ================================================
-- 更多多表查询 (Additional Multi-Table Queries) - 15条
-- ================================================

-- 61. 查询每个采购订单的总金额
SELECT po.`PurchaseOrderID`, s.`SupplierName`, SUM(pod.`Subtotal`) AS `OrderTotal`
FROM `PurchaseOrder` po
JOIN `Supplier` s ON po.`SupplierID` = s.`SupplierID`
JOIN `PurchaseOrderDetail` pod ON po.`PurchaseOrderID` = pod.`PurchaseOrderID`
GROUP BY po.`PurchaseOrderID`, s.`SupplierName`;

-- 62. 查询每个销售订单的详细信息
SELECT so.`SalesOrderID`, so.`SalesDate`, e.`Name` AS `CashierName`,
       sod.`ProductName`, sod.`Quantity`, sod.`Subtotal`
FROM `SalesOrder` so
JOIN `SalesOrderDetail` sod ON so.`SalesOrderID` = sod.`SalesOrderID`
JOIN `Product` p ON sod.`ProductID` = p.`ProductID`
JOIN `Employee` e ON so.`CashierID` = e.`EmployeeID`;

-- 63. 查询每个员工的销售总额
SELECT e.`Name`, SUM(so.`TotalAmount`) AS `TotalSales`
FROM `Employee` e
JOIN `SalesOrder` so ON e.`EmployeeID` = so.`CashierID`
GROUP BY e.`Name`;

-- 64. 查询每个供应商的库存情况
SELECT s.`SupplierName`, p.`ProductName`, p.`StockQuantity`
FROM `Supplier` s
JOIN `Product` p ON s.`SupplierID` = p.`SupplierID`;

-- 65. 查询每个类别的采购总额
SELECT c.`CategoryName`, SUM(pod.`Subtotal`) AS `TotalPurchased`
FROM `Category` c
JOIN `Product` p ON c.`CategoryID` = p.`CategoryID`
JOIN `PurchaseOrderDetail` pod ON p.`ProductID` = pod.`ProductID`
GROUP BY c.`CategoryName`;

-- 66. 查询每日采购数量
SELECT po.`PurchaseDate`, p.`ProductName`, SUM(pod.`Quantity`) AS `TotalQuantity`
FROM `PurchaseOrder` po
JOIN `PurchaseOrderDetail` pod ON po.`PurchaseOrderID` = pod.`PurchaseOrderID`
JOIN `Product` p ON pod.`ProductID` = p.`ProductID`
GROUP BY po.`PurchaseDate`, p.`ProductName`;

-- 67. 查询月度库存变化
SELECT MONTH(il.`Date`) AS `Month`, p.`ProductName`,
       SUM(il.`TotalIn`) AS `TotalIn`,
       SUM(il.`TotalOut`) AS `TotalOut`,
       SUM(il.`Balance`) AS `EndingBalance`
FROM `InventoryLedger` il
JOIN `Product` p ON il.`ProductID` = p.`ProductID`
WHERE YEAR(il.`Date`) = 2023
GROUP BY MONTH(il.`Date`), p.`ProductName`;

-- 68. 查询各支付方式的销售额
SELECT so.`PaymentMethod`, SUM(so.`TotalAmount`) AS `TotalSales`
FROM `SalesOrder` so
GROUP BY so.`PaymentMethod`;

-- 69. 查询特定日期范围内的销售订单
SELECT so.`SalesOrderID`, so.`SalesDate`, e.`Name` AS `CashierName`, so.`TotalAmount`
FROM `SalesOrder` so
JOIN `Employee` e ON so.`CashierID` = e.`EmployeeID`
WHERE so.`SalesDate` BETWEEN '2023-10-01' AND '2023-10-31';

-- 70. 查询某商品的库存变动历史
SELECT il.`Date`, il.`OperationType`, il.`TotalIn`, il.`TotalOut`, il.`Balance`, il.`OperationID`
FROM `InventoryLedger` il
WHERE il.`ProductID` = 1
ORDER BY il.`Date` DESC;

-- 71. 查询所有低库存商品及其供应商信息
SELECT p.`ProductName`, p.`StockQuantity`, p.`SafetyStock`, s.`SupplierName`
FROM `Product` p
JOIN `Supplier` s ON p.`SupplierID` = s.`SupplierID`
WHERE p.`StockQuantity` < p.`SafetyStock`;

-- 72. 查询每位员工生成的报表数量
SELECT e.`Name`, COUNT(r.`ReportID`) AS `ReportCount`
FROM `Employee` e
JOIN `Report` r ON e.`EmployeeID` = r.`GeneratedBy`
GROUP BY e.`Name`;

-- 73. 查询某供应商的活跃采购订单
SELECT po.`PurchaseOrderID`, po.`PurchaseDate`, po.`Status`
FROM `PurchaseOrder` po
WHERE po.`SupplierID` = 1 AND po.`Status` = '已到货';

-- 74. 查询每种支付方式的平均销售额
SELECT so.`PaymentMethod`, AVG(so.`TotalAmount`) AS `AverageSales`
FROM `SalesOrder` so
GROUP BY so.`PaymentMethod`;

-- 75. 查询库存调整原因统计
SELECT ia.`AdjustmentReason`, COUNT(*) AS `Count`
FROM `InventoryAdjustment` ia
GROUP BY ia.`AdjustmentReason`;

-- ================================================
-- 存储过程与视图补充 (Additional Stored Procedures and Views) - 5条
-- ================================================

-- 76. 创建存储过程：删除旧报表
DELIMITER //
CREATE PROCEDURE `DeleteOldReports` (
    IN p_CutoffDate DATE
)
BEGIN
    DELETE FROM `Report`
    WHERE `GenerationDate` < p_CutoffDate;
END //
DELIMITER ;

-- 77. 创建视图：采购订单汇总视图
CREATE VIEW `PurchaseOrderSummary` AS
SELECT po.`PurchaseOrderID`, s.`SupplierName`, po.`PurchaseDate`, po.`Status`,
       COUNT(pod.`DetailID`) AS `NumberOfItems`, SUM(pod.`Subtotal`) AS `TotalAmount`
FROM `PurchaseOrder` po
JOIN `Supplier` s ON po.`SupplierID` = s.`SupplierID`
JOIN `PurchaseOrderDetail` pod ON po.`PurchaseOrderID` = pod.`PurchaseOrderID`
GROUP BY po.`PurchaseOrderID`, s.`SupplierName`, po.`PurchaseDate`, po.`Status`;

-- 78. 创建存储过程：获取指定类别的库存总量
DELIMITER //
CREATE PROCEDURE `GetCategoryInventoryTotal` (
    IN p_CategoryID INT,
    OUT p_TotalStock INT
)
BEGIN
    SELECT SUM(`StockQuantity`) INTO p_TotalStock
    FROM `Product`
    WHERE `CategoryID` = p_CategoryID;
END //
DELIMITER ;

-- 79. 创建视图：销售订单汇总视图
CREATE VIEW `SalesOrderSummary` AS
SELECT so.`SalesOrderID`, so.`SalesDate`, e.`Name` AS `CashierName`,
       COUNT(sod.`DetailID`) AS `NumberOfItems`, SUM(sod.`Subtotal`) AS `TotalAmount`
FROM `SalesOrder` so
JOIN `Employee` e ON so.`CashierID` = e.`EmployeeID`
JOIN `SalesOrderDetail` sod ON so.`SalesOrderID` = sod.`SalesOrderID`
GROUP BY so.`SalesOrderID`, so.`SalesDate`, e.`Name`;

-- 80. 创建存储过程：获取员工工作详情
DELIMITER //
CREATE PROCEDURE `GetEmployeeWorkDetails` (
    IN p_EmployeeID INT
)
BEGIN
    SELECT po.`PurchaseOrderID`, po.`PurchaseDate`, po.`Status`
    FROM `PurchaseOrder` po
    WHERE po.`OperatorID` = p_EmployeeID;

    SELECT so.`SalesOrderID`, so.`SalesDate`, so.`TotalAmount`
    FROM `SalesOrder` so
    WHERE so.`CashierID` = p_EmployeeID;

    SELECT ia.`AdjustmentID`, ia.`AdjustmentQuantity`, ia.`AdjustmentReason`, ia.`AdjustmentDate`
    FROM `InventoryAdjustment` ia
    WHERE ia.`OperatorID` = p_EmployeeID;

    SELECT r.`ReportID`, r.`ReportType`, r.`GenerationDate`
    FROM `Report` r
    WHERE r.`GeneratedBy` = p_EmployeeID;
END //
DELIMITER ;
```

### 说明

上述SQL脚本涵盖了自选超市进销存管理系统的数据库创建、数据操作、查询、存储过程和视图的构建。以下是各部分的详细说明：

1. **数据库与表创建**：
    - **数据库**：创建名为`SupermarketInventory`的数据库，设置字符集为`utf8mb4`。
    - **表**：包括`Category`、`Supplier`、`Employee`、`Product`、`PurchaseOrder`、`PurchaseOrderDetail`、`SalesOrder`、`SalesOrderDetail`、`InventoryLedger`、`PriceLabel`、`InventoryAdjustment`、`Report`等表，涵盖进销存管理的各个方面。

2. **索引优化**：
    - 为常用查询字段添加索引，如`Product.Barcode`、`PurchaseOrder.SupplierID`等，以提升查询性能。

3. **示例数据插入**：
    - 插入了部分示例数据，包括类别、供应商、员工、商品、采购订单、销售订单等，以便于测试和演示。

4. **数据操作**（10条）：
    - 包括插入新采购订单、更新商品售价、删除停用供应商、插入销售订单明细、调整库存数量等操作。

5. **单表查询**（15条）：
    - 包括查询所有商品、活跃供应商、低库存商品、管理员、特定类别商品等。

6. **多表查询**（15条）：
    - 包括联结查询，如商品与类别、采购订单与供应商、销售订单与收银员等，及聚合查询如每日销售总额、每位收银员的销售总额等。

7. **存储过程与视图**（10条）：
    - 创建了多个存储过程，如`AddNewProduct`、`UpdateStock`、`GenerateDailySalesReport`等，自动化常见操作。
    - 创建了多个视图，如`CurrentInventory`、`SalesSummary`、`PurchaseOrderDetailsView`等，简化复杂查询。

8. **更多数据操作与查询**：
    - 插入和更新操作，如冻结员工账户、插入新的价格标签、删除库存调整记录等。
    - 更多复杂的多表查询，如采购订单汇总、销售订单详细信息、供应商绩效等。

### 关键点

- **事务管理**：存储过程`UpdateStock`确保库存更新的原子性，防止数据不一致。
- **视图的使用**：通过视图如`CurrentInventory`、`SalesSummary`等，简化复杂查询，提高查询效率。
- **存储过程的自动化**：存储过程如`AddNewProduct`、`GenerateDailySalesReport`等，自动化常见操作，减少手动错误。
- **索引的优化**：为常用的查询字段创建索引，显著提升查询性能。
- **数据完整性**：通过外键约束确保数据的一致性和完整性，避免孤立数据存在。

### 总结

通过上述SQL脚本的创建与优化，自选超市进销存管理系统的数据库结构得到了全面的构建与优化。该设计不仅满足当前业务需求，还具备良好的扩展性和维护性，能够支持未来业务的持续增长和变化。建议在实际实施过程中，结合具体业务场景进一步调整和优化数据库设计，以确保系统的高效运行与稳定性。