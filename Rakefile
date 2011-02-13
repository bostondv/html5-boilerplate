# Site deployment rakefile
# by: Boston Dell-Vandenberg
# http://pomelodesign.com
# 
# To deploy run the following commands from within the 
# root application directory
# 
#   $ rake staging deploy
#   $ rake production deploy
#   $ rake staging deploy:setup
#   $ rake staging deploy:pull
#

require 'net/ssh'
require 'open-uri'
require 'erb'
require 'date'

@application = "APPLICATION" # make sure your database, document root and repository match this.

desc "Deploy settings for staging server"
task :staging do
  @username = "pomelo" # for rsync deployment (eg. username)
  @host = "pomelodesign.com" # for rsync deployment (eg. domain)
  @destination = "~/webapps/#{@application}" # for rsync deployment (eg. ~/apps/application-name/)
end

desc "Deploy settings for production server"
task :production do
  @username = "USERNAME" # for rsync deployment (eg. username)
  @host = "DOMAIN" # for rsync deployment (eg. domain)
  @destination = "~/path/to/#{@application}" # for rsync deployment (eg. ~/apps/application-name/)
end

# You're done! No configuration needed below this line.

@repository = "git@pomelodesign.com:#{@application}.git"
@revision = "HEAD"
@media = "media"
@date_string = DateTime.now.strftime('%Y-%m-%d-%H-%M-%S')

def ssh(cmd)
  Net::SSH.start(@host, @username) do |session|
    session.exec cmd
  end
end

desc "Sets default deploy task"
task :deploy => "deploy:run"

desc "Deployment namespace"
namespace :deploy do
  
  desc "Deploys the site"
  task :run do
    puts "*** Deploying the site ***"
    deploy_command = [
      "cd #{@destination}",
      "git checkout -q master",
      #"git fetch",
      "git pull",
      "git reset --hard #{@revision}",
      #"git submodule update --init",
      "git branch -f deployed-#{@date_string} #{@revision}",
      "git checkout deployed-#{@date_string}"
    ].join(" && ")
    ssh deploy_command
    puts "*** Copying media files ***"
    system "rsync -avz #{@media}/  #{@username}@#{@host}:#{@destination}/#{@media}"
  end
  
  desc "Setup the site"
  task :setup do
    puts "*** Setting up the site ***"
    ssh "git clone #{@repository} #{@destination}"
  end

  desc "Pulls media files from remote server"
  task :pull do
    puts "*** Pulling down media files ***"
    system "rsync -avz #{@username}@#{@host}:#{@destination}/#{@media}/ #{@media}"
  end
  
end

# TODO: Cleanup staging deployment branches
  # $ DEPLOYED=`git branch -l | grep deployed`
  # $ for x in $DEPLOYED; do git branch -d $x; done