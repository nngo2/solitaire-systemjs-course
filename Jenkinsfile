stage ('CI') {
	node {

		checkout scm
		//git branch: 'jenkins2-course', 
		//   url: 'https://github.com/nngo2/solitaire-systemjs-course'

		// pull dependencies from npm
		// on windows use: bat 'npm install'
		bat 'npm install'

		// stash code & dependencies to expedite subsequent testing
		// and ensure same code & dependencies are used throughout the pipeline
		// stash is a temporary archive
		stash name: 'everything', 
			  excludes: 'test-results/**', 
			  includes: '**'
		
		// test with PhantomJS for "fast" "generic" results
		// on windows use: bat 'npm run test-single-run -- --browsers PhantomJS'
		bat 'npm run test-single-run -- --browsers PhantomJS'
		
		// archive karma test results (karma is configured to export junit xml files)
		step([$class: 'JUnitResultArchiver', 
			  testResults: 'test-results/**/test-results.xml'])
			  
	}
}

def notify(status){
    emailext (
	  from: "namngo2@gmail.com",
      to: "namngo2@gmail.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}


// demoing a second agent
//node('agent1') {
//    // on windows use: bat 'dir'
//    //sh 'ls'
//	bat 'dir'
//
//    // on windows use: bat 'del /S /Q *'
//    //sh 'rm -rf *'
//	bat 'del /S /Q *'
//
//    unstash 'everything'
//
//    // on windows use: bat 'dir'
//    //sh 'ls'
//	bat 'dir'
//}

//parallel integration testing
stage ('Browser Testing') {
	parallel chrome: {
		runTests("Chrome")
	}, firefox: {
		runTests("Firefox")
	}
}

def runTests(browser) {
    node {
        // on windows use: bat 'del /S /Q *'
        //sh 'rm -rf *'
		bat 'del /S /Q *'

        unstash 'everything'

        // on windows use: bat "npm run test-single-run -- --browsers ${browser}"
        //sh "npm run test-single-run -- --browsers ${browser}"
		bat "npm run test-single-run -- --browsers ${browser}"

        step([$class: 'JUnitResultArchiver', 
              testResults: 'test-results/**/test-results.xml'])
    }
}

node {
    notify("Deploy to staging?")
}

input 'Deploy to staging?'