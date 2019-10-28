$:.unshift( "lib" )
require "graphviz/constants"

require 'rubygems'
require 'rake/clean'
require 'bundler'
require 'rubygems/package_task'
require 'rake/testtask'
require 'fileutils'
require 'open-uri'
require 'yard'
include FileUtils

CLEAN.include ['**/.*.sw?', '*.gem', '.config', 'test/test.log']

desc "Packages up Ruby/GraphViz."
task :default => [:test, :package]
task :package => [:clean]

task :doc => :yard

YARD::Rake::YardocTask.new do |t|
end

Rake::TestTask.new(:test) do |t|
  t.libs << 'lib' << 'test'
  t.verbose = true

  require 'graphviz/utils'
  include GraphViz::Utils
  test_files = FileList['test/test_*.rb']
  test_files.exclude("test/test_examples.rb") unless find_executable("dot", nil)

  #p test_files
  t.test_files = test_files
end

desc "Generate man pages"
task :man do
  unless RUBY_PLATFORM == 'java'
    require 'ronn'
    chdir 'man' do
      sh "ronn -r *.ronn"
    end
  else
    puts "Can't generate man pages with JRuby"
  end
end

namespace :changelog do
  desc "Update CHANGELOG"
  task :update do
    require "github_changelog_generator"
    GitHubChangelogGenerator::ChangelogGenerator.new.run
  end
end

Bundler::GemHelper.install_tasks

namespace :contributors do
  desc "Install all-contributors-cli"
  task :install do
    sh "npm install --save-dev all-contributors-cli"
  end

  desc "Run"
  task :run do
    sh "npx all-contributors"
  end
end

def check_command(cmd)
  stderr = $stderr
  $stderr.reopen(File.new('/dev/null', 'w'))
  %x(which #{cmd})
  $stderr.reopen(stderr)
  $?.success?
end

desc 'Show ruby files'
task :tree do
  exit unless check_command('tree')
  system("tree -P '*rb' -I 'test*|sample*'")
end

desc 'Show largest files'
task :largest do
  require 'open3'
  # not working
  #Open3.pipeline("largest 400", "grep -v 'sample\|.png'")

  # grep is not working
  system("largest 400 | grep -v 'sample\|.png'")
end
