# Problem
Determine which LOPs node generated an item in the USD scene graph.

# Solution
```python
current_node = hou.node("/stage/OUT")
usd_stage = current_node.stage()
usd_prim = usd_stage.GetPrimAtPath("/scene/graph/path")
node_id = usd_prim.GetCustomDataByKey("HoudiniPrimEditorNodeID")
if node_id:
    editor_node = hou.nodeBySessionId(node_id)
    return editor_node
```