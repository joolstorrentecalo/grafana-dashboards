require 'dotenv'
require 'http'
require 'json'
require 'fileutils'

Dotenv.load('.env')

DASHBOARDS = File.expand_path('../dashboards', __FILE__)
TOKEN      = ENV['GRAFANA_API_TOKEN']
URL        = ENV['GRAFANA_URL'] + '/api'

http = HTTP.headers('Content-Type' => 'application/json')

if ENV['GRAFANA_API_USERNAME'] && !ENV['GRAFANA_API_USERNAME'].empty?
  http = http.basic_auth(
    user: ENV['GRAFANA_API_USERNAME'],
    pass: ENV['GRAFANA_API_PASSWORD'])
else
  http = http.auth("Bearer #{TOKEN}")
end

desc 'Exports all dashboards from Grafana'
task :export do
  response = http.get("#{URL}/search")

  if response.status == 200
    remotes = []

    JSON.parse(response.body.to_s).each do |meta|
      dash_response = http.get("#{URL}/dashboards/#{meta['uri']}")

      if dash_response.status == 200
        dash_json = JSON.parse(dash_response.body.to_s)
        file_name = "#{dash_json['meta']['slug']}.json"
        file_path = File.join(DASHBOARDS, file_name)

        body = dash_json['dashboard'].merge('id' => nil)

        File.open(file_path, 'w') do |handle|
          handle.write(JSON.pretty_generate(body))
        end

        remotes << file_name

        puts "Downloaded #{file_name}"
      else
        raise "Failed to get dashboard #{meta['uri']}: #{dash_response.reason}"
      end
    end

    # Remove any local dashboards no longer present in the remote Grafana
    # server. This ensures that if a dashboard is removed from Grafana it
    # doesn't longer around in our local ./dashboards directory.
    Dir[File.join(DASHBOARDS, '*.json')].each do |file|
      unless remotes.include?(File.basename(file))
        File.unlink(file)
      end
    end
  else
    raise "Failed to get the list of dashboards: #{response.reason}"
  end
end

desc 'Imports all local dashboards to Grafana'
task :import do
  Dir[File.join(DASHBOARDS, '*.json')].each do |file|
    basename = File.basename(file)
    payload  = { dashboard: JSON.parse(File.read(file)), overwrite: true }
    response = http.post("#{URL}/dashboards/db", body: JSON.dump(payload))

    if response.status == 200
      puts "Imported #{basename}"
    else
      raise "Failed to import #{basename}: #{response.reason}"
    end
  end
end
