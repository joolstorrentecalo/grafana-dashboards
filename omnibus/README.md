# GitLab Omnibus Dashboards

The Grafana dashbaord json files in this directory are tailored to the GitLab Omnibus install.

## [Overview Dashboard](overview.json)

This dashboard provides a basic overview of the health of a GitLab Omnibus install.

It can also be found on [grafana.com](https://grafana.com/dashboards/5774).

Note that this dashboard contains **Workhorse Latency** panel which is still
*work in progress* and at the moment it is not possible to make conclusions about
[Workhorse](https://docs.gitlab.com/ee/development/architecture.html#gitlab-workhorse)
performance based on it.

## [Gitaly](gitaly.json)

This dashboard provides an overview of the [Gitaly](https://docs.gitlab.com/ee/administration/gitaly/) service.

It can also be found on [grafana.com](https://grafana.com/dashboards/9123).
