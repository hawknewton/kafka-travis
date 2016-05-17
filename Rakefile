require 'kafka'
require 'socket'
require 'docker-api'

def ensure_running(name, tag, opts)
  begin
    container = Docker::Container.get(name)
    container.info['State']['Running'] && return
    container.remove
  rescue Docker::Error::NotFoundError
  end

  if opts['ports']
    port_bindings = opts.delete('ports').map do |port|
      ["#{port}/tcp", ["HostPort" => "#{port}"]]
    end
    host_config = { 'PortBindings' => Hash[*port_bindings.flatten(1)] }
    opts = opts.merge('HostConfig' => host_config)
  end

  puts "Starting container #{name}"

  container = Docker::Container.create(opts.merge('Image' => tag, 'name' => name))
  container.start
  sleep 5
end

task :default do
  ip = Socket.ip_address_list.detect{|intf| intf.ipv4_private?}.ip_address
  kafka_env = ["ADVERTISED_HOST=#{ip}", 'ADVERTISED_PORT=9292']
  ensure_running('kafka', 'spotify/kafka', 'Env' => kafka_env, 'ports' => [9092, 2181])
  puts "IP: #{ip}"
  puts `docker ps`
  kafka = Kafka.new seed_brokers: ["#{ip}:9092"]
  producer = kafka.producer
  producer.produce 'hello1', topic: 'test-messages'
  producer.deliver_messages
  producer.shutdown
end
