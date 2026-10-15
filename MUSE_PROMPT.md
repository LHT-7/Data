# 发给 Muse 的全量数据任务

日线数据已发布到私有 GitHub 仓库 `LHT-7/Data` 的 Release `quandata-tdx-daily-2026-10-15`：https://github.com/LHT-7/Data/releases/tag/quandata-tdx-daily-2026-10-15 。请通过你获得授权的 GitHub 连接下载 `Muse_metadata.zip`、`sh_daily.zip`、`sz_daily.zip`、`bj_daily.zip`。如果没有该私有仓库或 Release 附件的读取权限，请直接说明，不要声称已读取。元数据包内文档所称 `Muse_元数据说明包.zip` 就是 GitHub 上的 `Muse_metadata.zip`，内容完全相同。请先读取元数据包内的 `README.md`、`quality_report.json` 和 `transfer_manifest.csv`，再执行以下工作：

注意：`git clone` 不会下载 Release 附件，仓库根目录也没有 `daily/`。如可使用 GitHub CLI，可运行 `gh release download quandata-tdx-daily-2026-10-15 --repo LHT-7/Data --pattern '*.zip'`；解压市场 ZIP 后，`daily/` 才会出现。

1. 逐片确认接收情况：目标是 **80 个 Parquet 分片、29,855,757 行**。每片对照同名前缀的 `.quality.json` 核对实际行数及 SHA256。只报告已实际读取并核对的文件；未收到的列出文件名。
2. 读取 `anomalies_*.csv` 和 `source_inventory_*.csv`。建立明确的数据清洗规则，并报告哪些行、代码或时间段被排除。不要默默丢弃异常记录。
3. 建立有证据支持的**普通 A 股历史股票池**。源 `.day` 目录还含指数、基金等证券；不得将全部代码当作股票。若缺少证券类型及上市退市历史资料，先列出缺口与可核实的数据源。
4. 数据按 `symbol, trade_date` 排序，并处理重复日期。日期列是 YYYYMMDD 整数，价格是原始未复权价。
5. 在我提供复权因子或公司行动、停牌/涨跌停状态、历史股票池及交易规则之前，可做读取与流程验证，但不要把原始价格产生的收益率作为可投资的策略结论。
6. 为后续正式回测给出数据接口方案：行情、复权/公司行动、可交易状态、历史股票池和财报实际披露时间怎样关联；明确每项信号在何时可知、何时可成交。

请先回报上传完整性、数据质量与正式回测所缺数据，再开始参数测试。
