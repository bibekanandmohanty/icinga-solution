

node('R10k') {
  try {

    stage ('checkout scm') {
      checkout scm
    }
    stage ('clean up rpms') {
      sh "rm -f *.rpm"

    }
    stage ('create rpm') {
      echo 'Generating Icinga rules rpm'  
      sh "fpm -s dir -t rpm -d icinga2-common -n mobi-icinga-solution-rules-${env.BRANCH_NAME} --prefix /etc/icinga2/conf.d -v 1.0 --iteration `git rev-list HEAD --count` --directories ${env.BRANCH_NAME}-commands  --directories ${env.BRANCH_NAME}-hostgroups --directories ${env.BRANCH_NAME} ${env.BRANCH_NAME}-commands ${env.BRANCH_NAME}-hostgroups ${env.BRANCH_NAME}" 
    }

    stage ('publish rpm to pulp') {
      echo 'Publishing rpm to pulp'
      sh "pulp-admin login -u admin -p admin"
      echo 'Uploading RPM'
      sh "pulp-admin rpm repo uploads rpm --repo-id mobi-${env.BRANCH_NAME} -f mobi-icinga-solution-rules-${env.BRANCH_NAME}-1.0-`git rev-list HEAD --count`.x86_64.rpm"
      echo 'publishing rpm'
      sh "pulp-admin rpm repo publish run --repo-id mobi-${env.BRANCH_NAME}"
      echo 'logging out'
      sh "pulp-admin logout" 
    }

  }
  catch(err) {
    echo "One of the pipeline stages failed with the following error ${err}"
    
  }
}
