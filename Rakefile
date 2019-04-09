#Global Variables
$CURRENT_DIR = Rake.application.original_dir

# Helm Deployment Tasks
namespace :jarvis do 
    desc "Onboard all backend services to a CI/CD pipeline"
    task :deploy, [:service, :image] do |task, args|
        args.with_defaults(:image => "")
        image = ""
        unless args[:image].to_s.empty?
            image = "--set service.image=#{args[:image]}"
        end
        sh "helm upgrade --install #{args[:service]} base-chart/. -f service-data/#{args[:service]}/values.yaml \
        #{image} --recreate-pods --force"
    end

    desc "Deploy all services within service-data"
    task :recreate do
        directory = 'service-data'
        services = Dir.entries(directory).select {|entry| File.directory? File.join(directory,entry) and !(entry =='.' || entry == '..') }
        services.each do |service|
            Rake::Task["jarvis:deploy"].invoke(service)
            Rake::Task["jarvis:deploy"].reenable
        end
    end

    desc "List all releases"
    task :list do
        sh "helm list"
    end

    desc "Rollback a service"
    task :rollback, [:service] do |task, args|
        sh "helm rollback #{args[:service]} 0"
    end

end