require 'kafka'

task :default do
  puts "DOCKER_HOST: #{ENV['DOCKER_HOST']}"
  kafka = Kafka.new seed_brokers: ['localhost:9092']
#  kafka = Kafka.new seed_brokers: ['192.168.42.45:9092']
  producer = kafka.producer
  producer.produce 'hello1', topic: 'test-messages'
  producer.deliver_messages
  producer.shutdown
end
