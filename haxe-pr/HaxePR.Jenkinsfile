pipeline {
    agent any
    options {
        timeout(time: 15, unit: 'MINUTES')
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '5'))
    }

    parameters {
        string(name: 'GITHUB_PR', defaultValue: '', description: 'GitHub pull request to build')
    }
    environment {
        INSTALL_DIR = "$HOME/haxe/versions/pr-${params.GITHUB_PR}"
        PATH = "$PATH:$HOME/haxe/neko:$HOME/.opam/default/bin"
        OPAM_SWITCH_PREFIX = "$HOME/.opam/default"
        CAML_LD_LIBRARY_PATH = "$HOME/.opam/default/lib/stublibs:/usr/local/lib/ocaml/4.05.0/stublibs:/usr/lib/ocaml/stublibs"
        OCAML_TOPLEVEL_PATH = "$HOME/.opam/default/lib/toplevel"
        HAXE_BRANCH_MAP = "pull/${params.GITHUB_PR}/head:pr-${params.GITHUB_PR}"
        HAXE_BRANCH = "pr-${params.GITHUB_PR}"
    }
    stages {
        stage('checkout') {
            steps {
                sh '''
                rm -rf haxe
                git clone https://github.com/HaxeFoundation/haxe.git
                cd haxe
                git fetch origin $HAXE_BRANCH_MAP
                git checkout $HAXE_BRANCH
                git submodule update --init --recursive
                '''
            }
        }
        stage('build Haxe PR') {
            steps {
                sh '''
                cd haxe 
                make clean
                make
                '''
            }
        }
                
        stage('install Haxe') {
            steps {
                sh '''
                mkdir -p $INSTALL_DIR
                cd haxe
                cp -va haxe haxelib std $INSTALL_DIR
                '''
            }
        }

        stage('trigger benchmarks') {
            steps {
                build(job: 'Benchmarks/cases%2Fbcrypt', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmarks/cases%2Fbinarytrees', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmarks/cases%2Fdox', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmarks/cases%2Fformatter', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmarks/cases%2Fformatter_noio', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmarks/cases%2Fjson', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmarks/cases%2Fmandelbrot', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmarks/cases%2Fmandelbrot_anon_objects', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmarks/cases%2Fnbody', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmarks/cases%2Fsha256', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmarks/cases%2Fsha512', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmarks/cases%2Falloc', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
            }
        }
    }
}
