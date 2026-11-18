> **下载：** 完整归档在 [最新 release](../../releases/latest) 中，文件名 `ConvGRU_NDVI_reproducibility_archive.zip`（约 101 MB）。本仓库只放说明、许可证、引用信息和校验值；代码、权重、窗口索引和结果表都在 ZIP 内。下载后可用 `SHA256SUMS.release` 校验。

# ConvGRU NDVI 缺测恢复｜代码与实验数据索引

以 `work/ndvi_recovery_sealed_v2/frozen_plan.json` 为封存主评价依据。ConvLSTM 跨模型回放与无 Daymet 封存回放各有独立计划，属于封存揭盲后的补充分析。所有原实验文件保留原字节与相对路径。

| 文件或目录 | 内容 |
| --- | --- |
| `portable.py`、`run.ps1`、`project/`、`environment/` | 入口、模型、训练与回放、数据准备和环境记录 |
| `checkpoint_inventory.csv` | 36 个最佳权重，各自的组别、种子、最佳轮次、SHA-256 与训练计划哈希。最后一列标明权重哈希是否另有已核验的下游计划登记 |
| `frozen_hash_audit.csv` | 冻结计划与关联计划的文件级校验。封存主计划的 35 个代码条目全部匹配 |
| `artifacts/paper300/prepared/audited_manifests/` | 原审核训练与开发清单，SHA-256 均与冻结计划匹配。训练 122,553 个 H8 窗口，开发 9,452 个 |
| `window_index/sealed_matched_windows.csv` | 11,315 条封存配对窗口，每条以 PID 与首释放日期为联合键，包含 4、8、16 期缺口对应的三个预测起点 |
| `window_index/sealed_matched_windows_by_gap.csv` | 同一队列的长表按缺口展开为 33,945 行 |
| `work/ndvi_recovery_sealed_v2/` | 封存清单、空间审核、执行记录、12 份种子回放缓存及指标表 |
| `work/ndvi_recovery_weather_v1/`、`work/ndvi_recovery_cross_model_v1/cuda/` | 无 Daymet 及跨模型的原计划、回放缓存与汇总结果 |
| `work/*/formal/runs/`、`work/ndvi_recovery_weather_v1/runs/` | 每轮训练历史及最终的 run.json。原主实验与 Paper300 可找到的 .log 保留在各自原目录 |
| `SHA256SUMS` | ZIP 内文件的校验清单，在解压根目录运行 `sha256sum -c SHA256SUMS` |

窗口表使用原封存分片和原 `cohorts_and_coverage` 函数导出。导出时核验了封存清单、分片中所有登记数组的 SHA-256、90 县×2 年的 180 条县年覆盖记录，以及汇总中的队列大小和覆盖县数。主队列覆盖 89 县；有至少 12 期后续观测的子队列有 6,458 条、86 县。`max_reported_followup` 为 8 或 12，`available_followup_periods` 为原窗口实际可用后续期数。导入 CSV 时将 FIPS 设为文本，保留前导零。

这是用于核对代码、模型、队列及指标的归档。完整训练和像元回放依赖原迁移包的训练/开发分片与封存工作目录。本 ZIP 储存清单与窗口索引，约 11 GB 的训练/开发数组、约 3.6 GB 的封存数组、原始遥感/天气归档和训练断点留在项目本地。未发现这些后续实验单独保存的终端 stdout/stderr 文件，逐轮训练历史和结构化回放缓存随包附上。

需要重新导出索引时，将 `export_sealed_windows.py` 放在有原分片的迁移包根目录，用实验 Python 经 `portable.py` 导入运行。

```bash
python -c 'import portable, runpy; runpy.run_path("export_sealed_windows.py", run_name="__main__")' ./exported_windows
```

本次整理未重新训练或评分。完整分片运行方法见 `project/ndvi_recovery_sealed_v2/README.md`。
