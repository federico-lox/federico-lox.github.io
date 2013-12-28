task :default => :serve

task :serve , :host do |t, args|
  host = args[:host] || "127.0.0.1"
  sh "jekyll serve --watch --host #{host}"
end

task :build do
  sh "jekyll build --trace"
end

task :clean do
	sh "rm -Rf ./_site"
end
