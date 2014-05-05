# squeeze

Square + Guice = Squeeze

A dependency injection based web development framework for Ruby.

## Usage

    # service_module.rb
    require 'actions/homepage_action'
    
    class ServiceModule < Squeeze::Module
      def configure
        install(AnotherModule)
        bind(:user_service).to(UserService)
        bind(:item_persistence).to(PersistenceFactory.new(Item)) # Imaginary persistence library
        bind_actions(
          HomepageAction
        )
      end

      def config_provider()
        YAML.load_file('config/config.yml')
      end
      provide :config, with: :config_provider
    end

    # actions/homepage_action.rb
    class HomepageAction < Squeeze::Action
      route '/', '/index.*'

      inject :user_service, :item_persistence, :config
      def initialize(user_service, item_persistence, config)
        @user_service = user_service
        @item_persistence = item_persistence
        @config = config
      end

      def handle(request)
        render(:home)
      end
    end

    # service.rb
    require 'service_module'

    class Service < Squeeze::Service
      module ServiceModule
    end

And run with:

    squeeze service.rb
