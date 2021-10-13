# Problem
Connect a parameter to a function located in the `PythonModule` of the node.

# Solution
- Edit the type properties for the node.
- In the `parameters` tab select the parameter.
- Pick the `channels` tab from the right-hand set of tabs.
- Under defaults select the icon that looks like a calculator ("Literal"), change it to
python. In the adjoinging parameter enter the required python expression - ie. 
`hou.phm().some_function()`.

# Notes
- `hou.phm` is just shorthand for `hou.pwd().hm()`.
- If the node instance is needed in the script `hou.phm().another_function(hou.pwd())`.