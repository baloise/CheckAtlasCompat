lib = library identifier: 'WorkflowLibsShared@master', retriever: modernSCM(
	[$class: 'GitSCMSource', remote: 'https://git.balgroupit.com/CICD-DevOps/WorkflowLibsShared.git']
  )  

import groovy.json.JsonSlurper

appKeys = [
    'com.baloise.confluence.digital-signature',
    'com.baloise.confluence.dashboard-plus'
    ]

node {

    String manageURL = 'https://marketplace.atlassian.com/manage/vendors/1211530/addons'
    String cb = http_get('https://my.atlassian.com/download/feeds/current/confluence.json')
    String latest = new JsonSlurper().parseText((cb.trim() - 'downloads(')[0..-2])[0].version
    println "latest confluence version is " + latest
    appKeys.each {
        stage(it) {
            mpApiUrl = "https://marketplace.atlassian.com/rest/2/addons/$it/versions/latest"
            println "checking "+mpApiUrl
            String max = new JsonSlurper().parseText(http_get(mpApiUrl)).compatibilities.find{it.application == 'confluence'}.hosting.server.max.version 
            println 'found ' + max
            if(max != latest) {
				      mail to:fog('kDBuW/GgxJOUa0vUEESs1BsZhdhDx6pbT75O63KHcts='), subject: "update $it to $latest ", body: "see <a href=\"$manageURL\">Marketplace</a>",  mimeType: "text/html"
			      }
        }    
    }
}
