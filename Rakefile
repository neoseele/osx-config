require 'logger'
$LOG_GLOBAL = Logger.new(STDOUT)

# Homebrew prefix (SED escaped):
homebrew_prefix='\/Users\/nmiu\/.homebrew'
homebrew_repo='\/Users\/nmiu\/.homebrew\/Homebrew'

# Packages to install
homebrew_packages = ['wget',
                     'mtr',
                     'autojump',
                     'zsh-syntax-highlighting',
                     'ack',
                     'watch',
                     'fzf',
                     'mpv']
cask_packages = ['skype',
                 'alfred',
                 'atom',
                 'dropbox',
                 'flux',
                 'sourcetree',
                 'iterm2-beta',
                 'rubymine']

## Cask packages that are not installed into ~/Applications and don't need to be zapped
cask_package_exceptions = ['ksdiff']

# Git settings
git_config_global_user_name='Neil Miao'
git_config_global_user_email='nmiu@google.com'
git_config_global_push_default='simple'
git_config_global_core_excludesfile='~/.gitignore_global'

#desc 'Install the whole shebang'
task :install => [:'preinstall:all', :'homebrew:install', :'cask:install', :'config:all', :'git:configure']

namespace :preinstall do

  desc 'Run all preinstall tasks'
  task :all => [:update_submodules]

  desc 'Recursively update all submodules'
  task :update_submodules do
    $LOG_GLOBAL.info('Setting up git submodules...')
    system('git submodule update --init --recursive')
  end

  desc 'Install Xcode CLI tools'
  task :xcode_select do
    $LOG.info('Installing Xcode CLI tools...')
    system('xcode-select --install')
  end
end

namespace :homebrew do

  desc 'Install Homebrew, Cask and packages'
  task :install => [:install_homebrew, :install_homebrew_packages]

  desc 'Install Homebrew'
  task :install_homebrew do
    $LOG_GLOBAL.info('Installing Homebrew...')
    system('curl -L https://raw.githubusercontent.com/Homebrew/install/master/install -o /tmp/homebrew_install.rb')
    system("sed -i .bak 's/HOMEBREW_PREFIX = .*/HOMEBREW_PREFIX = \"#{homebrew_prefix}\".freeze/' /tmp/homebrew_install.rb")
    system("sed -i .bak 's/HOMEBREW_REPOSITORY = .*/HOMEBREW_REPOSITORY = \"#{homebrew_repo}\".freeze/' /tmp/homebrew_install.rb")
    system('ruby /tmp/homebrew_install.rb')
    #TODO: This is not working right, need to have something stop here and ask to reset the terminal
    #+ separate the stages of the install into stage1/stage2
    `source ~/.zshrc`
  end

  desc 'Install Homebrew packages'
  task :install_homebrew_packages do
    $LOG_GLOBAL.info('Installing Homebrew packages...')
    homebrew_packages.each do |package|
       system("brew install #{package}")
    end
  end

end

namespace :cask do
  desc 'Install Cask and packages'
  task :install => [:install_cask_packages ]

  task :install_cask_packages do
    $LOG_GLOBAL.info('Installing Cask packages...')
    cask_packages.each do |package|
      system("brew cask install --require-sha #{package}")
    end

    $LOG_GLOBAL.info('Installing Cask exceptions')
    cask_package_exceptions.each do |package|
      system("brew cask install #{package}")
    end
  end

end

namespace :git do
  desc 'Set git settings'
  task :configure => [:set_identity, :set_defaults]

  desc 'Set Git identity'
  task :set_identity do
    $LOG_GLOBAL.info('Setting up git identity...')
    system("git config --global user.name #{git_config_global_user_name}")
    system("git config --global user.email #{git_config_global_user_email}")
  end

  desc 'Set Git defaults'
  task :set_defaults do
    $LOG_GLOBAL.info('Setting up git settings...')
    system("git config --global push.default #{git_config_global_push_default}")
    system("git config --global core.excludesfile #{git_config_global_core_excludesfile}")
    system("git config --global include.path .gitaliases")
  end
end

namespace :config do
  desc 'Set all configs'
  task :all => [:mac_defaults]

  task :mac_defaults do
    $LOG_GLOBAL.info('Setting up Mac defaults. This will require your password...')
    system('./configs/defaults_mac.sh')
  end

end
