[![Keenton : Informatique / Cloud / Infogérance / SaaS](https://www.keenton.com/keenton_github.png)](https://www.keenton.com)

## Zabbix Template - pfSense
FreeBSD template pfSense-oriented:
  - complete remodeling of memory monitoring
  - metrics from the memory buffer used by the network (_MBUF_)
  - metrics on State Table usage
  - improved visibility of graphics
  - revised triggers

#### We talk about it on our blog
[PfSense Monitoring with Integrated Zabbix Agent](https://www.keenton.com/supervision-pfsense-avec-agent-zabbix-integre/)

#### Requirements
  - _admin_ access on Zabbix server and firewall pfSense

#### Zabbix Server
Nothing special, just import both templates, create the host for pfSense and link it to the appropriate template (active or passive).

#### Zabbix Agent: _User Parameters_
In Zabbix agent config on pfSense, you must display the advanced configuration options and add to the _User Parameters_ field that just appeared:
    UserParameter=pfsense.states.max,grep "limit states" /tmp/rules.limits | cut -f4 -d ' '
    UserParameter=pfsense.states.current,grep "current entries" /tmp/pfctl_si_out | tr -s ' ' | cut -f4 -d ' '
    UserParameter=pfsense.mbuf.current,netstat -m | grep "mbuf clusters" | cut -f1 -d ' ' | cut -d '/' -f1
    UserParameter=pfsense.mbuf.cache,netstat -m | grep "mbuf clusters" | cut -f1 -d ' ' | cut -d '/' -f2
    UserParameter=pfsense.mbuf.max,netstat -m | grep "mbuf clusters" | cut -f1 -d ' ' | cut -d '/' -f4

Commands used:
  - `grep`: filter text by pattern.
  - `netstat`: show network status.
  - `cut`: cut out selected portions of each line.
  - `tr`: replace characters (used here to replace the space sequences with a single space, necessary in some cases to properly use the `cut` command).

##### _Timeout!_
In Zabbix Agent config (on pfSense) you may need to increase the timeout value (set to 3 seconds by default). It is certainly the first thing to check on Zabbix Agent if you find yourself unable to retrieve some _User Parameters_ metrics.

#### Template App Zabbix Agent
The link with this template has been remove for the "Active" version does not exist by default in Zabbix. Do not forget to add it in the linked templates.

_ps: you may create an "Active" template version by cloning the "Passive" one and changing the type of items._

