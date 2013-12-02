---
layout: userguide
subject: 2. Installation
prev: 1. Overview
prev_link: /overview
---

### <a name="requirements"></a>2.1. Software Requirements 

JRE 6+ is required to run JRuyi.

### <a name="install"></a>2.2. Installing JRuyi

Please go to [Download]({{ site.url }}/download) to get the latest release of JRuyi.  Unpack the archive that you downloaded, and then you will get a directory named as jruyi-x.x.x (_x.x.x_ is the version number of JRuyi.  For example, if the version number of JRuyi that you downloaded is 1.0.0, then _jruyi-1.0.0_ will be the directory name.).  We use **$JRUYI_HOME** to refer to this directory as the home directory of JRuyi in the rest of this guide.

### <a name="startstop"></a>2.3. Starting/Stopping JRuyi

To start JRuyi, go to $JRUYI_HOME and run the following command.

```bash
bin/ruyi &
```
To stop JRuyi, go to $JRUYI_HOME and run the following command.

```bash
bin/ruyi-cli shutdown
```

### <a name="cli"></a>2.4. JRuyi CLI

To start JRuyi CLI, go to $JRUYI_HOME and run the following command.

```bash
bin/ruyi-cli
```

To see all the installed OSGi bundles, run the following command.

```bash
default> bundle:list
```

To exit JRuyi CLI, enter _exit_ or _quit_.

