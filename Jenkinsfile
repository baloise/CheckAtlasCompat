lib = library identifier: 'WorkflowLibsShared@master', retriever: modernSCM(
	[$class: 'GitSCMSource', remote: 'https://git.balgroupit.com/CICD-DevOps/WorkflowLibsShared.git']
  )  

import groovy.json.JsonSlurper

appKeys = [
    'com.baloise.confluence.digital-signature',
    'com.baloise.confluence.dashboard-plus'
    ]

manageURL = 'https://marketplace.atlassian.com/manage/vendors/1211530/addons'

def versionComparator(String a, String b) {
  // see https://gist.github.com/founddrama/971284#file-versioncomparator-groovy
  def VALID_TOKENS = /._/
  a = a.tokenize(VALID_TOKENS)
  b = b.tokenize(VALID_TOKENS)
  
  for (i in 0..<Math.max(a.size(), b.size())) {
    if (i == a.size()) {
      return b[i].isInteger() ? -1 : 1
    } else if (i == b.size()) {
      return a[i].isInteger() ? 1 : -1
    }
    
    if (a[i].isInteger() && b[i].isInteger()) {
      int c = (a[i] as int) <=> (b[i] as int)
      if (c != 0) {
        return c
      }
    } else if (a[i].isInteger()) {
      return 1
    } else if (b[i].isInteger()) {
      return -1
    } else {
      int c = a[i] <=> b[i]
      if (c != 0) {
        return c
      }
    }
  }
  
  return 0
}

node {
    String cb = http_get('https://my.atlassian.com/download/feeds/current/confluence.json')
    String latest = new JsonSlurper().parseText((cb.trim() - 'downloads(')[0..-2])[0].version
    println "latest confluence version is " + latest
    appKeys.each {
        stage(it) {
            mpApiUrl = "https://marketplace.atlassian.com/rest/2/addons/$it/versions/latest"
            println "checking "+mpApiUrl
            String max = new JsonSlurper().parseText(http_get(mpApiUrl)).compatibilities.find{it.application == 'confluence'}.hosting.server.max.version 
            println 'found ' + max
            if(versionComparator(max, latest) < 0) {
				  println 'sending mail'
			      mail to:fog('kDBuW/GgxJOUa0vUEESs1BsZhdhDx6pbT75O63KHcts='), subject: "update $it to $latest ", body: "see <a href=\"$manageURL\">Marketplace</a>",  mimeType: "text/html"
			}
        }    
    }
}
