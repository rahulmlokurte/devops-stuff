This document contains the best practices which must be followed for Helm.

## Chart Names
- Chart Names must be **lower case letter** and **numbers**. Words must be separated by **dashes(-)**:
- Neither **uppercase** nor **underscores** can be used in chart names.
- **Dots** should not be there in chart names.
- The directory that contains a chart **MUST** have same name as chart.

## Values
- Variable names should begin with a lowercase letter and words should be separated with camelcase.

**Correct :**
```yaml
camel: true
camelCase: true
```

**Incorrect :**
```yaml
Camel: true
camel-case: true
```
Note that all of Helm's built-in variables begin with an uppercase letter to easily distinguish them from user-defined values: **.Release.Name, .Capabilities.KubeVersion.**

- values may be flat or Nested

Nested : 
```yaml
server:
  name: nginx
  port: 80
```

Flat :
```yaml
serverName: nginx
serverPort: 80
```
- Flat should be favoured over nested.
- Make the types clear. For example, _**bar: false**_ is not the same thing as _**bar: "false"**_
