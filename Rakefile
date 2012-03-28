require 'rubygems'
require 'rake'

PACKAGE_NAME = "effective-test-java"
INSTALL_DIR = "/srv/effective-test-java"
TOP_DIR = File.expand_path(File.join(File.dirname(__FILE__)))
BUILD_DIR = File.expand_path(File.join(File.dirname(__FILE__), "build"))
SRC_DIR = File.expand_path(File.join(File.dirname(__FILE__), "src"))
FAKE_INSTALL_DIR = File.join(BUILD_DIR, INSTALL_DIR)

build_id = ENV.has_key?("BUILD_ID") ? ENV["BUILD_ID"].dup : "1"
build_id.gsub!('-', '')

task :clean do
  sh("rm -rf #{BUILD_DIR}")
  sh("rm -f *.rpm")
  sh("rm -f *.deb")
end

directory BUILD_DIR
directory FAKE_INSTALL_DIR

desc "Build the war file"
file "dbapp.war" => FileList.new(File.join(SRC_DIR, "/**/*")) do |t|
  sh("cd #{SRC_DIR}; zip -r #{File.join(FAKE_INSTALL_DIR, 'dbapp.war')} .")
end

task :schema => [ BUILD_DIR, FAKE_INSTALL_DIR ] do
  sh("cp #{File.join(TOP_DIR, "schema.sql")} #{FAKE_INSTALL_DIR}")
end

desc "Build the rpm"
task :rpm => [ "dbapp.war", :schema ] do
  sh("fpm -s dir -t rpm -n #{PACKAGE_NAME} -v #{build_id} -C #{BUILD_DIR} .")
end

desc "Build the deb"
task :deb => [ "dbapp.war", :schema ] do
  sh("fpm -s dir -t deb -n #{PACKAGE_NAME} -v #{build_id} -C #{BUILD_DIR} .")
end

task :default => [ :clean, BUILD_DIR, FAKE_INSTALL_DIR, :rpm, :deb ] 
