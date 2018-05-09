# Grafana JSON Dashboards

This repository contains sample dashboards that will display various GitLab
performance metrics obtained from Prometheus or InfluxDB. See
[GitLab CE][gitlab-ce-docs] or [GitLab EE][gitlab-ee-docs] for helping
configuring/installing Prometheus, InfluxDB, Grafana and these dashboards.

## Importing/Exporting Dashboards

To make it easier to import/export dashboards this repository contains a
Rakefile that can take care of this. For this to work Ruby 2.x is required.

First make sure Bundler is installed. If you don't know what Bundler is or it's
not installed run the following to install it:

    gem install bundler --no-ri --no-rdoc

Next install all the required dependencies:

    bundle install

Next you'll need to get two things:

1. The URL of your Grafana instance (including the HTTP scheme)
2. A Grafana API token that has the "Editor" permission. See
   <http://docs.grafana.org/reference/http_api/> for information on creating API
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

## Provided Dashboards

The dashboards residing in `./omnibus` are curated dashboards used to monitor
Omnibus installs.

The dashboards residing in `./dashboards` are _all_ dashboards used to monitor
GitLab.com.

## InfluxDB Requirements

Some dashboards may depend on certain [continuous queries][continuous-queries]
and retention policies to be set up. For more information on these requirements
see <http://doc.gitlab.com/ce/monitoring/performance/grafana_configuration.html>
under "Apply retention policies and create continuous queries".

## Contributing

Please see the [contribution guidelines](CONTRIBUTING.md).

[gitlab-ce-docs]: http://docs.gitlab.com/ce/monitoring/performance/introduction.html
[gitlab-ee-docs]: http://docs.gitlab.com/ee/monitoring/performance/introduction.html
[continuous-queries]:https://docs.influxdata.com/influxdb/latest/query_language/continuous_queries/
