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
        ADD_REVISION = 1
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
                rm -rf "$INSTALL_DIR"
                mkdir -p "$INSTALL_DIR"
                cd haxe
                cp -va haxe haxelib std "$INSTALL_DIR"
                '''
            }
        }

        stage('trigger benchmarks') {
            steps {
                build(job: 'Benchmark_bcrypt', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmark_binarytrees', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmark_dox', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmark_formatter', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmark_formatter_noio', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmark_json', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmark_mandelbrot', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmark_mandelbrot_anon_objects', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmark_nbody', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmark_sha256', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmark_sha512', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
                build(job: 'Benchmark_alloc', parameters: [string(name: 'HAXE_BRANCH', value: env.HAXE_BRANCH)], wait: false)
            }
        }
    }
}
