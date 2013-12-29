---
layout: page
name: Getting Started
---

# Deploying Your Bundle

### 1. Start JRuyi

Go to $JRUYI_HOME and run the following command.

```bash
bin/ruyi
```

### 2. Deploy Your Bundle

To deploy and start your bundle, please run the following command.

```
bin/ruyi-cli bundle:start your-bundle-url
```

For example, if your bundle locates at $JRUYI_HOME/bundles/your-bundle.jar, then the command will look like this.

```
bin/ruyi-cli bundle:start ${jruyi.bundle.base.url}your-bundle.jar
```

${jruyi.bundle.base.url} will be substituted to the URL of the directory *$JRUYI_HOME/bundles/*

