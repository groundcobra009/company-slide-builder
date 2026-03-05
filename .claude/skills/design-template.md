# Skill: design-template

## Description
Detects requests to create or customize a slide design template (colors, fonts, style) for a company or brand. Routes to the planner agent.

## Trigger
Keywords: "デザインテンプレート", "テンプレート作成", "カラー変更", "ブランドカラー", "design template", "company template", "モノトーン", "配色"

## Behavior
1. Collect the user's input: company name, URL, brand keywords, or color preferences.
2. If no input is provided, ask the user for a company name or URL (or confirm monotone default).
3. Spawn the `design-template-planner` agent with the collected context.

## Routing
```
User Request → design-template-planner agent
```

## Integration
- This skill can be used standalone to customize template.js branding.
- The `slide-builder` skill can also trigger this skill's agents (brand-researcher + template-generator) internally as Step 0.
- After running this skill, `slide-builder` will use the updated template.js colors.

## Important
- This skill contains NO design logic.
- This skill contains NO research logic.
- All planning and execution happen in agents.
