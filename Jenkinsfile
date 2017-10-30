/*******************************************************************************
 * Copyright (c) 2012-2017 TypeFox GmbH and itemis AG.
 * All rights reserved. This program and the accompanying materials
 * are made available under the terms of the Eclipse Public License v1.0
 * which accompanies this distribution, and is available at
 * http://www.eclipse.org/legal/epl-v10.html
 *
 * Contributors:
 *   Moritz Eysholdt - Initial contribution and API
 *******************************************************************************/
timestamps() {
    String mailTo = 'jakub.siberski@numberfour.eu'
    try {

        // tell Jenkins how to build projects from this repository
        node() {

            def mvnHome = tool 'apache-maven-3.0.5'
            def mvnParams = '--batch-mode --update-snapshots -fae -Dmaven.repo.local=xpect-local-maven-repository -DtestOnly=false'
            timeout(time: 3, unit: 'HOURS') {
                stage('compile with Eclipse Luna and Xtext 2.9.2') {
                    sh "${mvnHome}/bin/mvn -P!tests -Dtarget-platform=eclipse_4_4_2-xtext_2_9_2 ${mvnParams} clean install"
                    archive 'org.eclipse.xpect.releng/p2-repository/target/repository/**/*.*'
                }

                wrap([$class: 'Xvnc', useXauthority: true]) {

                    stage('test with Eclipse Luna and Xtext 2.9.2') {
                        sh "${mvnHome}/bin/mvn -P!plugins -P!xtext-examples -Dtarget-platform=eclipse_4_4_2-xtext_2_9_2 ${mvnParams} clean integration-test"
                        junit '**/TEST-*.xml'
                    }

                    stage('test with Eclipse Mars and Xtext nighly') {
                        sh "${mvnHome}/bin/mvn -P!plugins -P!xtext-examples -Dtarget-platform=eclipse_4_5_0-xtext_nightly ${mvnParams} clean integration-test"
                        junit '**/TEST-*.xml'
                    }
                }
            }
        }

        mail(to: mailTo,
                subject: "${env.JOB_NAME} (${env.BUILD_NUMBER}) succeeded",
                body: "${env.BUILD_URL} succeeded - ${env.JOB_NAME} (#${env.BUILD_NUMBER}).")
    } catch (exc) {
        mail(to: mailTo,
                subject: "${env.JOB_NAME} (${env.BUILD_NUMBER}) failed",
                body: "${env.BUILD_URL} is failing - ${env.JOB_NAME} (#${env.BUILD_NUMBER}). The following exception was caught : \n ${exc.toString()}")
        //rethrow otherwise job will always be green
        throw exc
    } finally {
        //cleanup
    }
}
