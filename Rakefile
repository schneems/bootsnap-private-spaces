require_relative "config/application"

Rails.application.load_tasks

task "assets:precompile" do
  ENV.sort.each {|(key, value)| puts "#{key}=#{value}"}
  puts "Build directory is #{Dir.pwd} should be /app"

  command = "lscpu"
  puts "List processor information with #{command.inspect}:"
  puts `#{command}`
end
