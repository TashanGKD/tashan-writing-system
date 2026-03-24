# 贡献指南 / Contributing Guide

感谢你对他山宣传写作体系的关注！

---

## 如何贡献

### Skill 改进

1. Fork 本仓库
2. 在 `skills/[skill-name]/SKILL.md` 中进行修改
3. 在 Skill 文件末尾的「变更记录」中追加一条记录（格式：`### v版本 — 日期 — 一句话说明`）
4. 提交 Pull Request，标题格式：`fix(skill-name): 一句话说明` 或 `feat(skill-name): 新增功能`

### 文档改进

- 修改 `docs/writing-guides/` 中的规范文档
- 若修改了写作风格手册，请在文档末尾追加变更记录

### Bug 反馈

请在 Issues 中提交，包含：
- 触发场景（你对 AI 说了什么）
- 预期行为
- 实际行为
- 相关 Skill 名称

---

## 代码规范

- Skill 文件使用 Markdown，frontmatter 格式：`---\nname: xxx\ndescription: xxx\n---`
- 变更记录追加在文件末尾，不修改已有内容
- API Key 等敏感信息一律用 `YOUR_XXX_API_KEY` 占位符替代

---

## License

贡献的内容将在 MIT License 下发布。
