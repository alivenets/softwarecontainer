# -*- mode: ruby -*-
# vi: set ft=ruby :

vagrant_private_key_file="vagrant_key"

Vagrant.configure(2) do |config|
    config.vm.box = "debian/jessie64"
    #config.vm.box = "pelagibuild"

    # Deploy a private key used to clone gits from pelagicore.net
    config.vm.provision "file", source: vagrant_private_key_file, destination: "/home/vagrant/.ssh/id_rsa"

    # Workaround for some bad network stacks
    config.vm.provision "shell", privileged: false, path: "cookbook/utils/keepalive.sh" 

    # Upgrade machine to testing distro & install build dependencies
    config.vm.provision "shell", path: "cookbook/deps/testing-upgrade.sh"
    config.vm.provision "shell", path: "cookbook/deps/common-build-dependencies.sh"

    # Add known hosts
    config.vm.provision "shell", privileged: false, path: "cookbook/system-config/pelagicore-ssh-conf.sh"

    # Install dependencies via git
    config.vm.provision "shell", privileged: false, 
        args: ["ivi-logging", "https://github.com/Pelagicore/ivi-logging.git"],
        path: "cookbook/build/cmake-git-builder.sh"

    config.vm.provision "shell", privileged: false, 
        args: ["pelagicore-utils", "git@git.pelagicore.net:development-tools/pelagicore-utils.git"],
        path: "cookbook/build/cmake-git-builder.sh"

    config.vm.provision "shell", privileged: false, 
        args: ["ivi-main-loop", "https://github.com/Pelagicore/ivi-main-loop.git"],
        path: "cookbook/build/cmake-git-builder.sh"

    config.vm.provision "shell", privileged: false, 
        args: ["jsonparser", "git@git.pelagicore.net:pelagicore-utils/jsonparser.git"],
        path: "cookbook/build/cmake-git-builder.sh"

    # Build and install project
    config.vm.provision "shell", privileged: false, 
        args: ["pelagicontain", "git@git.pelagicore.net:application-management/pelagicontain.git", "-DENABLE_DOC=1 -DBUILD_TESTS=ON -DENABLE_COVERAGE=1"],
        path: "cookbook/build/cmake-git-builder.sh"


    # Run an example (note, running as root)
    config.vm.provision "shell", privileged: false, inline: <<-SHELL
        cd pelagicontain/examples/simple
        cmake .
        make
        sudo ./launch.sh -b session
    SHELL
end