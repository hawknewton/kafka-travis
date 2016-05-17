require 'kafka'
require 'socket'

task :default do
  ip = Socket.ip_address_list.detect{|intf| intf.ipv4_private?}.ip_address
  puts `docker run -p 2181:2181 -p 9092:9092 --env ADVERTISED_HOST=#{ip} --env ADVERTISED_PORT=9092 -d spotify/kafka`
  sleep 5
  puts "IP: #{ip}"
  puts `docker ps`
  kafka = Kafka.new seed_brokers: ["#{ip}:9092"]
  producer = kafka.producer
  producer.produce 'hello1', topic: 'test-messages'
  producer.deliver_messages
  producer.shutdown
end
