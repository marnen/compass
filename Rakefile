if ENV['RUN_CODE_RUN']
  # We need to checkout edge haml for the run>code>run test environment.
  if File.directory?("haml")
    Dir.chdir("haml") do
      sh "git", "pull"
    end
  else
    sh "git", "clone", "git://github.com/nex3/haml.git"
  end
  $LOAD_PATH.unshift "haml/lib"
end

require 'rubygems'
require 'rake'
require 'lib/compass'

# ----- Default: Testing ------

task :default => :run_tests

require 'rake/testtask'
require 'fileutils'

Rake::TestTask.new :run_tests do |t|
  t.libs << 'lib'
  t.libs << 'haml/lib' if ENV["RUN_CODE_RUN"]
  test_files = FileList['test/**/*_test.rb']
  test_files.exclude('test/rails/*', 'test/haml/*')
  t.test_files = test_files
  t.verbose = true
end
Rake::Task[:test].send(:add_comment, <<END)
To run with an alternate version of Rails, make test/rails a symlink to that version.
To run with an alternate version of Haml & Sass, make test/haml a symlink to that version.
END

begin
  require 'jeweler'
  Jeweler::Tasks.new do |gemspec|
    gemspec.rubyforge_project = "compass"
    gemspec.name = "compass"
    gemspec.summary = "A Real Stylesheet Framework"
    gemspec.email = "chris@eppsteins.net"
    gemspec.homepage = "http://compass-style.org"
    gemspec.description = "Compass is a Sass-based Stylesheet Framework that streamlines the creation and maintainance of CSS."
    gemspec.authors = ["Chris Eppstein"]
    gemspec.has_rdoc = false
    gemspec.add_dependency('haml-edge', '>= 2.1.12')
  end
rescue LoadError
  puts "Jeweler not available. Install it with: sudo gem install technicalpickles-jeweler -s http://gems.github.com"
end


desc "Compile Examples into HTML and CSS"
task :examples do
  linked_haml = "tests/haml"
  if File.exists?(linked_haml) && !$:.include?(linked_haml + '/lib')
    puts "[ using linked Haml ]"
    $:.unshift linked_haml + '/lib'
  end
  require 'haml'
  require 'sass'
  require 'pathname'
  require 'lib/compass'
  require 'lib/compass/exec'
  FileList['examples/*'].each do |example|
    puts "\nCompiling #{example}"
    puts "=" * "Compiling #{example}".length
    # compile any haml templates to html
    FileList["#{example}/*.haml"].each do |haml_file|
      basename = haml_file[0..-6]
      engine = Haml::Engine.new(open(haml_file).read, :filename => haml_file)
      puts "     haml #{File.basename(basename)}"
      output = open(basename,'w')
      output.write(engine.render)
      output.close
    end
    Dir.chdir example do
      Compass::Exec::Compass.new(["--force"]).run!
    end
  end
end

namespace :git do
  desc "Perform normal operations required for pushing to github."
  task :push => [:manifest, :gem]  do
    sh "git", "add", "Manifest", "compass.gemspec", "VERSION"
    sh "git", "commit", "-m", "Updated Manifest and gemspec."
    sh "git", "push", "origin", "master"
  end
  task :clean do
    sh "git", "clean", "-fdx"
  end
end

task :manifest => :"git:clean"