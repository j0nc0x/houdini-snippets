# Summary
HDA callbacks. In short, don't use them, or at least use them sparsely. This is especially important when a HDA is closely tied to an external module.

## What?
When writing an HDA it might be useful to initialise some data or process when a HDA is loaded or is created. If this is something core to the functionallity of the node, then it would be wise to avoid the events that can be defined in the node's Type Properties (ie. like this https://www.sidefx.com/docs/houdini/hom/locations.html#asset_events). Some (possibly all, but certainly the important ones like OnLoaded / OnCreated) of these callbacks will not run when the HDA is nested inside another locked HDA. Ideally when creating a HDA we want it to work in all contexts, so we want to avoid this.

To make these callbacks run when inside a locked asset we essentially have two options:
- Run the code needed to initialise from the parents callback (or the parent's parent if that node is also locked).
- Avoid using these callbacks altogether - ie. python scripts on a parm or menu script do run inside a locked HDA, so code can be node initialisation can also be done here.

## Example
Say we have a HDA `mynode.hda` that uses the module `MyClass` and needs to initalise it on loaded / created. We want to be able to nest this node at any level within 'N' numbner of parent HDAs, so we are going to avoid using the callbacks as discussed above.

### Callbacks Approach
First lets look at the approach which uses HDA callbacks. This will fail when the node is embedded within another locked HDA.

`PythonModule`
```python
#!/usr/bin/env python
import my_class

def initialise_node(current_node):
    # Initialise the class and store it in the cachedUserData
    current_node.setCachedUserData(my_class.MyClass())
```


We can then use this `initialise_node` function to create the class either when the node is created or loaded (ie. when a scene is loaded).

`OnCreated`
```python
#!/usr/bin/env python

current_node = kwargs["node"]
current_node.hm().initialise_node(current_node)
```
`OnLoaded`
```python
#!/usr/bin/env python

current_node = kwargs["node"]
current_node.hm().initialise_node(current_node)
```

The `MyClass` module might then be used on some parameters, for example a parm python callback and a parm menu script python callback.

### Solution
Instead lets not use the `OnCreated` / `OnLoaded` callbacks. We will use the same `PythonModule` function in this example.

`PythonModule`
```python
#!/usr/bin/env python
import my_class

def initialise_node(current_node):
    # Initialise the class and store it in the cachedUserData
    current_node.setCachedUserData(my_class.MyClass())
```
