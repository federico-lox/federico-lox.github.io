task :default => :server

task :server , :response do |t, args|
  host = args[:response] || "127.0.0.1"
  sh "jekyll serve --watch --host #{host}"
end

task :build do
  sh "jekyll build --trace"
end