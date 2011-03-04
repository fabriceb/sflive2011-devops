!SLIDE subsection

# Give developers the power to deploy themselves #
## Scripted deployment ##



!SLIDE incremental

# Deployment #

<br />
<br />
<br />

* Deployment is a very critical task usually done by admins
* Remember Murphy's law: "If anything can go wrong, it will"
* When things go wrong, most of the time developers have the solution
* So give the developers the responsibility to deploy, rollback, correct and deploy again!


!SLIDE small

# Scripting deployment can be VERY easy
## Simple Fabric script example ##
      
	@@@ python
    # fabfile.py
    from fabric.api import *
    env.hosts = ['theodo@myserver.com']

    def deploy():
      with cd('/theodo/sflive2011'):
        run('git pull')
        run('./symfony doc:build --all --no-confirmation')
        run('./symfony cc')


    $ fab deploy


!SLIDE smaller

# A good practise: scripting a rollback
## Another Fabric example ##

	@@@ python
    # fabfile.py
    def deploy():
      tag = "prod/%s" % strftime("%Y/%m-%d-%H-%M-%S")
      local('git tag -a %s -m "Prod"' % tag)
      local('git push --tags')
      with cd(path):
        run('git fetch')
        tag = run('git tag -l prod/* | sort | tail -n1')
        run('git checkout ' + tag)

    def rollback(num_revs=1):
      with cd(path):
        run('git fetch')
        tag = run('git tag -l prod/* | sort | tail -n' + \
              str(1 + int(num_revs)) + ' | head -n1')
        run('git checkout ' + tag)
