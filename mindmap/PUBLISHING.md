# 每日 Bot 资讯脑图 — 生成流程

Page: canvas_id `a9310f67-1516-42ec-99c6-57619508586e`
URL: https://cursor.com/canvas/store-225dfb2c-b2b5-4b46-b344-98bacc217290/a9310f67-1516-42ec-99c6-57619508586e
每天覆盖同一个 Page（WriteCanvas 带 canvas_id，标题里的日期用 EditCanvas 或在 Page title 里改）。不要新建。

## 步骤
1. ReadTranscript（in=<bot id>, limit 6~10）读今天各 bot 的最终输出（SendToUser 正文 / sent_draft 邮件正文）：
   - 科技公司高管最新观点监测 2a081a03-7a3f-4d03-b357-bfa85e694dc4
   - PC行业动向每日洞察 2b75fd03-f3fd-4d0a-be2b-9069de66c521
   - Github top star 洞察 474f271e-314f-4fde-8620-bfe0ec3ed193（趋势 Page bc088540-4131-4f1e-81cc-bf3a07a369bf）
   - X TOP new idea 5977d8ea-ddc0-4d71-a5c3-b6625026e342
   - Apple新闻洞察 788b8393-4980-4514-a3bb-e8ea4c274aa9
   - AI大拿 最新言论追踪 f7f2f19a-fa9c-4ad5-a9dd-010c07936624
   - 洞察专家 fe922d76-90b2-465e-977e-03b6f2fdae72（通常无日更，今天无输出就跳过）
   - 旺财早报 /workspace/briefs/ai-brief-YYYY-MM-DD.md
   只收当天（Asia/Shanghai）的内容；当天没输出的 bot 不放。
2. ListCanvases(updated_after=当天 00:00 UTC+8) 找各 bot 当天写的报告 Page，作为"详细报告"链接；没有则链到 grokbot://app/v1/agent?id=<id>。
3. 取原图：对每条来源文章 curl -sL -A "Mozilla/5.0" URL | grep og:image / twitter:image，记录到 images.txt；把 `&amp;` 换成 `&`；只用 https；取不到就不放图（X 帖、财报站通常没有可用图）。绝不编造。
4. 整理 DATA：每个主题 3–6 条 {t 标题, s 一句话结论, d 2–4 句详情, img?, links}；挑 3 条放 TOP（今日最值得关注，偏 PC OS / AI 桌面 / Apple）。
5. ReadCanvas 现有源码，替换 TOP/DATA 常量和标题日期后 WriteCanvas(canvas_id=上面的 id) 覆盖。组件结构（useState 展开/选中、useGrokTheme 配色）不用改。

## 6. 发布到 GitHub

把当天脑图做成可公开分享的静态页，推到 `kingangbian-prog/AIDashboard`，不要改仓库根目录的 `index.html`（那是多 Agent 看板）。

1. 模板：以仓库里已有的 `mindmap/2026-11-06.html` 为模板（自包含单文件 HTML+CSS+JS，白底深字，顶部三条「今日最值得关注」、可展开主题、点要点看详情/原图/来源）。只替换标题、摘要、TOP/DATA 对应的页面内容，不编造任何文字或图片。
2. 命名：新文件写成 `mindmap/YYYY-MM-DD.html`（例如 `mindmap/2026-11-11.html`）。数据以当天 Canvas 的 TOP/DATA 为准。`grokbot://` 链接可以保留；同一条里如果有可在浏览器打开的 `https` 来源，必须一并放上，不要用假的 https 顶替。
3. 更新 `mindmap/index.html`，用相对路径跳到最新一天（`<meta http-equiv="refresh" content="0; url=YYYY-MM-DD.html">` 和页面里的 `<a href="YYYY-MM-DD.html">`，不要写成绝对网址），这样分享一个固定入口就总是最新。底部或入口页加往期日期链接（例如 2026-11-11、2026-11-06）。
4. 提交到 `main`（仓库允许就直接提交；不行就开 PR 并合并）。不要动根目录看板。可选：同步改仓库 README 里脑图的「本期」链接。
   提交方式说明：2026-11-06 那次是 Cursor Cloud Agent 在分支上提交后开 PR，由 cursor[bot] 合并进 main（https://github.com/kingangbian-prog/AIDashboard/pull/1）。本机 `gh` 若已登录，也可用 `gh`/`git` 直接推 `main`。`cursor-github` 连接器能读仓库、也显示有 push 权限，但没有创建或更新文件的工具，不能单独完成这次提交。2026-11-11 的页面已生成在 `/workspace/mindmap-publish/out/2026-11-11.html` 和 `/workspace/mindmap-publish/out/index.html`，待写入仓库。
5. 尝试用 GitHub API 打开 Pages：source 为 `main` 分支，path 为 `/`（root）。开不了就记下原因，请用户在仓库 Settings → Pages 里选 Deploy from branch / `main` / `/ (root)`。
6. 验证分享网址：先 `curl` `https://kingangbian-prog.github.io/AIDashboard/mindmap/`；若 404，再用 `https://raw.githack.com/kingangbian-prog/AIDashboard/main/mindmap/index.html` 和对应的 `https://raw.githack.com/kingangbian-prog/AIDashboard/main/mindmap/YYYY-MM-DD.html`，应返回 200 且 `content-type` 为 `text/html`。因为 index 用的是相对路径，在 githack 下也会跳到同一目录里正确的日期文件。
