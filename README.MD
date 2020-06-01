ygrep is a tool capable of recursively searching a directory full
of yaml files and finding those which match a yaml based input 
pattern.


For example, you can provide as input a yaml pattern:
```yaml
kind: Deployment
metadata:
  name: release-controller
```

and ygrep will search through a specified directory, reporting
whether it can find any yaml files with keys matching the input
pattern.

For example it will match a file containing:
```yaml
kind: Deployment
apiVersion: apps/v1
metadata: 
  annotations:
    x: 'hello'
  name: release-controller
  ...
...    
```

Remember that yaml is a superset of json, so you could also specify
the input pattern on the command line:
```bash
ygrep -p '{ kind: "Deployment", metadata: {name: "release-controller" }}' .
```

ygrep supports multi-document yaml and is also Kubernetes aware (i.e.
if a file is found to contains a Kubernetes kind "List", ygrep will
search match each individual element of that list). 

Therefore, ygrep would also find the following file matched the 
example input search pattern. 
```yaml
---
..yaml subdoc..
---
kind: List
apiVersion: v1
- kind: Deployment
  apiVersion: apps/v1
  metadata: 
    annotations:
      x: 'hello'
    name: release-controller
  ...
---
..yaml subdoc..
---
...etc 
```

When it comes to matching yaml lists, the tool considers a list to
match when it contains a subset of an identically nested / named
list in the pattern.

Consider this search pattern:
```yaml
custom_yaml: 
  additional_list:
    - item1
    - item3
```

It will match a file containing:
```yaml
custom_yaml: 
  additional_list:
    - item1
    - item2
    - item3
    - item4
  more_content: xyz
```