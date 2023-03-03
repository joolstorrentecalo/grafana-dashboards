# Grafana JSON Dashboards

This repository contains sample dashboards that will display various GitLab
performance metrics obtained from Prometheus or InfluxDB. See
[GitLab CE][gitlab-ce-docs] or [GitLab EE][gitlab-ee-docs] for helping
configuring/installing Prometheus, InfluxDB, Grafana and these dashboards.

## Provided Dashboards

|Directory|Import Method(s)|
|---------|-------------|
|`omnibus/`|Import via Grafana Web interface|
|`dashboards/`|Grafana API, Rake task, Import via Grafana Web interface (see below)|

### Curated Omnibus dashboards

The dashboards residing in `omnibus/` are curated dashboards used to
monitor Omnibus installs. These dashboards assume a datasource named `GitLab Omnibus`. They can be imported via the `Import` button, or via provisioning APIs.

### GitLab.com dashboards

The dashboards residing in `dashboards/` are _all_ dashboards used to
monitor GitLab.com at https://dashboards.gitlab.com. Note that certain amount of
customization is required to make these dashboards work for your instance.
GitLab does not provide support for these dashboards, but you are more than
welcome to play around on your own and make them work in your environment. If you
would like specific one to be supported, please
[open an issue in omnibus-gitlab project](https://gitlab.com/gitlab-org/omnibus-gitlab/issues)
while sharing details about your use case and we will try to make them fit to
all `omnibus/` instances as soon as possible.

You can install these in several ways:

1. Using the bulk Rake task (see below)
1. Using the [Grafana Create / Update dashboard API](http://docs.grafana.org/http_api/dashboard/#create-update-dashboard)
1. Converting the files into the right format and importing them via the Grafana Web interface.

Alternatively, we recommend reviewing the [GitLab Infrastructure Monitoring Mixins](#gitlab-infrastructure-monitoring-mixins---customizable-dashboards-and-rules) (see below).

### GitLab Infrastructure Monitoring Mixins - Customizable Dashboards and Rules

The GitLab.com Infrastructure team maintains Prometheus recording rules, alerts, and dashboards for GitLab.com in a Jsonnet format within the [GitLab Infrastructure Runbooks Project](https://gitlab.com/gitlab-com/runbooks/). This source code generates the JSON dashboards available in this project.

In addition to the dashboards for GitLab.com, we also maintain a set of Prometheus recording rules, alerts, and dashboards that are **better suited for single-tenant and self-managed installations**. These provide several advantages over using GitLab.com dashboards directly.

The Reference Architecture Mixins provide recording rules and alerts for a GitLab environment, using the same Service Level Monitoring approach that we use on GitLab.com. These mixins are well-tested with large reference architecture installations on **[GitLab Dedicated](https://docs.gitlab.com/ee/subscriptions/gitlab_dedicated/)**.

#### Links

* **GitLab Infrastructure Monitoring Mixins** <https://gitlab.com/gitlab-com/runbooks/-/tree/master/reference-architectures/get-hybrid>
* **Generated Grafana Dashboards and Prometheus Configuration**: <https://gitlab.com/gitlab-com/runbooks/-/tree/master/reference-architectures/get-hybrid/src>

![Reference Architecture Triage Dashboard](./img/grafana-triage.png)

Here are the benefits of using the GitLab Infrastructure Monitoring Mixins over using the GitLab.com json dashboards directly:

1. They follow the [Jsonnet mixin](https://monitoring.mixins.dev/) approach, **allowing customizations for other environments to be easily made** before the dashboards are generated. These customizations will flow through to alerts and dashboards. Instructions on how to customize the dashboards and rules can be found here: <https://gitlab.com/gitlab-com/runbooks/-/tree/master/reference-architectures>. 
1. The mixins **use the same Service Level Monitoring and Alerting** that we use on GitLab.com. 
1. The mixins **use the same underlying alerting configuration as GitLab.com**, so they're kept up-to-date by GitLab's Infrastructure team with regular improvements and tweaks to cover more features.

Please note that these dashboards are not officially supported, but you're welcome to use them and provide feedback.

## Bulk Importing/Exporting GitLab.com Dashboards

To make it easier to import/export dashboards this repository contains a
Rakefile that can take care of this. For this to work Ruby 2.x is required.

First make sure Bundler is installed. If you don't know what Bundler is or it's
not installed run the following to install it:

    gem install bundler --no-document

Next install all the required dependencies:

    bundle install

Next you'll need to get two things:

1. The URL of your Grafana instance (including the HTTP scheme)
2. A Grafana API token that has the "Editor" permission. See
   <https://grafana.com/docs/grafana/latest/http_api/> for information on creating API
   tokens. Make sure the API token is linked to the correct Grafana organization if
   you're using multiple organizations.

Once you have these run the following command:

    cp .env.example .env

Now edit `.env` using your favourite editor and make sure that `GRAFANA_URL` and
`GRAFANA_API_TOKEN` contain the right values.

Once configured we can get started. To download all dashboards from a remote
Grafana instance run `bundle exec rake export`. This command will download the
dashboards into `./dashboards` and _remove_ any dashboards in that directory
that are not present in the remote Grafana instance.

To import dashboards into a Grafana instance simply run `bundle exec rake
import`. This will _overwrite_ any existing dashboards with the same title.

### Converting GitLab.com dashboards to Import format

Attempting to import JSON files in `dashboards/` via the Grafana Web
interface will result in a blank dashboard unless the files are
converted to the right format. This conversion can also be done via
`jq`. For example:

```sh
jq .dashboard dashboards/gitaly-fleet-overview.json > import/import-gitaly-fleet-overview.json
```

This repository also contains a Rake task that can convert all these
files and save them in `import/`:

```
bundle exec rake convert
```

## InfluxDB Requirements

Some dashboards may depend on certain [continuous queries][continuous-queries]
and retention policies to be set up. For more information on these requirements
see <https://docs.gitlab.com/ce/administration/monitoring/performance/grafana_configuration.html>
under "Apply retention policies and create continuous queries".

## Contributing

Please see the [contribution guidelines](CONTRIBUTING.md).

[gitlab-ce-docs]: https://docs.gitlab.com/ce/administration/monitoring/performance/index.html
[gitlab-ee-docs]: https://docs.gitlab.com/ee/administration/monitoring/performance/index.html
[continuous-queries]: https://docs.influxdata.com/influxdb/v2.0/upgrade/v1-to-v2/migrate-cqs/
