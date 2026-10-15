# QuanData 通达信扩展数据交付（2026-10-15 快照）

此包来自 `D:/QuanData/02_股票数据包` 和 `D:/QuanData/03_财务数据包`，与已发布的日线包分开。它们是通达信“专业财务数据”下载页所称的股票数据包和财务数据包：<https://www.tdx.com.cn/article/stockfin.html>。

**本交付保留原始数值和位置编号，不提供已核实的业务字段字典，也没有历史实际披露时间。当前只适合数据接收核对、字段研究和回测接口设计，不可直接用于正式策略收益评估。**

## 第二包：股票数据包

- 9,300 个 `gp{sh,sz,bj}*.dat`，其中 1 个空文件。全部文件大小均为 13 字节的整数倍。
- 共 129,742,317 条记录，导出为 145 个 Snappy Parquet 分片，按市场与原始字段编号命名；Parquet 合计 1,161,007,425 字节。
- 单片按 Parquet 元数据统计的最大未压缩列数据为 109,188,126 字节（`sz_field_27.parquet`），低于 500 MB。
- 13 字节原始布局：`uint8 field_id`、`uint32 date_raw`、`float32 value_1`、`float32 value_2`，均按小端读取。Parquet 包含证券代码和源文件内从零开始的 `source_row_index`，便于追溯。
- 原始字段编号出现 1–52 和 99；**字段含义、单位、日期含义尚未核实**。这不是已证明的分钟 OHLCV 数据。
- `gpszsh.txt` 列出 9,022 个文件，这些文件的 MD5 和大小均匹配；另有 278 个本地 `.dat` 未列在该清单中，已单独标注。
- 949 条记录的 `date_raw` 不符合八位 `YYYYMMDD` 形状，其中部分为 0 或六位数。359 处相邻同字段日期非递增。这可能与不同字段的数据格式有关，**不能统一丢弃**；详见 `stockpkg/anomalies.csv`。
- 具体源文件 SHA256、行数、清单状态见 `stockpkg/source_inventory.csv`；逐片行数和 SHA256 见 `stockpkg/stockpkg_manifest.csv` 与同名前缀 `.quality.json`。

Parquet 字段：`symbol`、`field_id_raw`、`observation_date_raw`、`value_1_raw`、`value_2_raw`、`source_row_index`。文件名如 `stockpkg/shards/sh_field_01.parquet`。`observation_date_raw` 是中性名称，**不能直接视为财报披露日期或信号可知时间**。

## 第三包：财务数据包

- 148 期 `gpcw{YYYYMMDD}.zip`，所有 ZIP 的 MD5 和大小与 `gpcw.txt` 清单一致。
- 其中 122 期有记录，26 期为空包。非空期共 303,597 条公司期别记录，导出为 122 个 Snappy Parquet 文件，合计 314,039,588 字节。
- 财务单片最大未压缩列数据为 13,203,402 字节（`gpcw20231231.parquet`）。
- 非空期每条记录有 584 个原始 `float32` 字段，保守命名 `f000` 至 `f583`。`report_period` 是报告期，**不是实际披露日期**；本包没有历史修订版本或完整公告时间。
- `gpcw20120930.dat` 的本地已解压副本与同名 ZIP 内文件有 764,569 字节不同。导出采用与 `gpcw.txt` 的 MD5 相符的 ZIP 内文件，并保留两份 SHA256 和差异计数；见该期 `.quality.json`。
- 报告期内有 92 条额外重复代码记录，未删除；定位见 `finance/duplicate_codes.csv`。原始索引中的 `source_flag_raw` 保留原值，不推断交易所。
- 具体来源、行数和逐片 SHA256 见 `finance/finance_manifest.csv` 和各期 `.quality.json`。

Parquet 字段：`report_period`、`code`、`source_flag_raw`、`source_record_index`、`f000`–`f583`。文件名如 `finance/reports/gpcw20250630.parquet`。26 个空包仅在清单和质量报告中列出，不生成空 Parquet。

## 接收与使用

1. 先核对 Release 中每个 ZIP 的 SHA256，再按两个 manifest 逐片核对 Parquet SHA256 和实际行数。市场包中的 `.quality.json` 与 Parquet 同前缀。
2. 阅读两个异常明细和 `anomaly_summary.json`。保留全部原始记录，任何清洗必须记录被排除的证券、字段、日期和原因。
3. 第二包的字段编号与第三包的 `fNNN` 都需独立取得官方定义或用可靠原始报告逐项交叉核对；未经核实不进入正式策略。
4. 财报策略还需历史实际披露时间、历史修订版本、复权/公司行动、可交易状态和历史证券池。此交付无法单独解决这些缺口。

重建脚本：`export_stockpkg.py`、`export_finance.py`、`audit_anomalies.py`。原始 `D:/QuanData` 文件未修改。
