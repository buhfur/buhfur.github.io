---
title: Linux Kernel Log levels
parent: Misc
grand_parent: Linux
nav_order: 60
layout: default
---

# Log Levels

* KERN_EMERG (0): System is unusable. Highest severity, indicating system instability or imminent crash.

* KERN_ALERT (1): Action must be taken immediately. Requires immediate user attention.

* KERN_CRIT (2): Critical conditions. Informs of critical hardware or software failures.

* KERN_ERR (3): Error conditions. Used for non-critical errors, often by drivers.

* KERN_WARNING (4): Warning conditions. Indicates potential problems that should be addressed. This is often the default console log level.

* KERN_NOTICE (5): Normal but significant condition. Used for notable but non-serious events, such as security-related messages.

* KERN_INFO (6): Informational messages. Provides general information, such as startup messages or driver initialization details.

* KERN_DEBUG (7): Debug-level messages. Contains detailed information primarily useful for debugging purposes and typically only appears if debugging is enabled during compilation.
