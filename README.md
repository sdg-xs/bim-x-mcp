# BIM X MCP

BIM workflow resources for dimension-led modeling in Autodesk Revit through the BIMweight/Revit MCP.

## BIMMCP skill

The reusable skill is in [`skills/bimmcp`](skills/bimmcp). It plans work by evidence, dependency, and BIM category; uses Revit MCP for model operations; and reserves computer use for Revit approval dialogs that the user has authorized.

Use `$bimmcp` with the drawing set and requested categories. The skill's planning guide is at [`skills/bimmcp/references/model-planning.md`](skills/bimmcp/references/model-planning.md).
