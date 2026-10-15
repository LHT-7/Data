# QuanData TDX 日线快照（2026-10-15）

供 Muse 读取和核对的 A 股市场原始日线转换包。数据来自 `D:/QuanData/01_京沪深日线_TDX`，转为按市场、年份划分的 Snappy Parquet。数据包含指数、基金等非普通 A 股证券；正式股票策略须另建历史股票池、复权及可交易状态。

## Release 附件

| 附件 | SHA256 | 内容 |
|---|---|---|
| `Muse_metadata.zip` | `357f9b5042af7eed6e49239e46866af90925443506730d84c82e7b89c76787ac` | README、交付清单、质量报告、源清单、异常清单和脚本 |
| `sh_daily.zip` | `fc6ea047d6d2853dc2766a8ad1fc94485ccc35a31f9e2bd4a66bd8f257c49cd2` | 沪市 37 片及逐片质量报告 |
| `sz_daily.zip` | `5dddc9b701369c9468d10f94477e88f4d1c42735cc7316f386db370e6d85ccb7` | 深市 36 片及逐片质量报告 |
| `bj_daily.zip` | `5b16d58b85301f04ef8cf17e63ec26ad9d3b0d275c6b40c95276aef548b8f472` | 北交所 7 片及逐片质量报告 |

总计 80 个 Parquet 分片、29,855,757 行。逐片 SHA256 和行数在 `Muse_metadata.zip` 内的 `transfer_manifest.csv` 中；每片也有同名前缀的 `.quality.json`。所有 ZIP 内的日线文件都保留 `daily/` 目录结构。元数据包内文档沿用本地文件名 `Muse_元数据说明包.zip`，与 GitHub 上的 `Muse_metadata.zip` 是同一份字节内容。

仓库为私有仓库。Muse 需要获得该仓库的读取权限，才能下载 Release 附件。

## Muse 核对要求

下载四个附件后，先验上表的附件 SHA256，再解压并逐片核对 `transfer_manifest.csv` 中的 SHA256、行数和 80 片是否齐全。对异常记录按 `anomalies_*.csv` 定位，不要悄悄删除。原始价未复权，回测收益率只可用于流程演示。
