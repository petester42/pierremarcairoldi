namespace :deploy do
  desc "Deployment to production"
  task :production do
    sh 'bundle exec s3_website push --config-dir="./production"'
  end

  desc "Deployment to staging"
  task :staging do
    sh 'bundle exec s3_website push --config-dir="./staging"'
  end

  desc "Deploys to staging and production"
  task :all do
    sh 'rake deploy:production'
    sh 'rake deploy:staging'
  end

  desc "Deploy if Travis environment variables are set correctly"
  task :travis do
    branch = ENV['TRAVIS_BRANCH']
    pull_request = ENV['TRAVIS_PULL_REQUEST']
    
    abort 'Must be run on Travis' unless branch
    
    if pull_request != 'false'
      puts 'Skipping deploy for pull request; can only be deployed from master branch.'
      exit 0 
    end

    if branch != 'master'
      puts "Skipping deploy for #{ branch }; can only be deployed from master branch."
      exit 0
    end

    sh 'rake deploy:all'
  end
end

namespace :publish do
  desc "Build and deploy to production"
  task :production do
    sh 'rake build'
    sh 'rake deploy:production'
  end

  desc "Build and deploy to staging"
  task :staging do
    sh 'rake build'
    sh 'rake deploy:staging'
  end

  desc "Build and deploy to both staging and production"
  task :all do
    sh 'rake build'
    sh 'rake deploy:all'
  end
end

desc "Build site locally"
task :build do
  sh 'bundle exec jekyll build'
end

desc "Start server"
task :server do
  puts "Starting server"

  jekyll = Process.spawn("bundle exec jekyll serve")

  trap("INT") {
    Process.kill(9, jekyll) rescue Errno::ESRCH
    exit 0
  }

  Process.wait(jekyll)
end 

task :default => :server