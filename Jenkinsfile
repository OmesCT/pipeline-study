pipeline {
    agent any
    
    environment {
        // แยกค่าตัวแปรไว้เพื่อให้จัดการง่าย (เหมือนการเตรียมวัตถุดิบแยกไว้เป็นหมวดหมู่)
        APP_NAME = 'pipeline-demo-app'
        DOCKER_REGISTRY = 'my-docker-registry.local'
        STAGING_SERVER = 'staging-server-01'
        PROD_SERVER = 'prod-server-01'
    }
    
    stages {
        stage('Build & Static Analysis') {
            steps {
                echo '🛠️ [Quality Check] Building and testing the application...'
                // จำลองการทำ Unit Test และ Security Scan (การชิมรสชาติอาหารก่อนเสิร์ฟ)
                sh 'echo "Running Unit Tests..." && sleep 2' 
                sh 'echo "Running SonarQube Static Analysis..." && sleep 2'
            }
        }

        stage('Package & Push') {
            steps {
                echo '📦 [Packaging] Creating Docker Image...'
                // Build Once, Deploy Anywhere: ทำ Image ครั้งเดียวใช้ได้ทุก Env
                sh "echo 'docker build -t ${DOCKER_REGISTRY}/${APP_NAME}:latest .'"
                sh "echo 'docker push ${DOCKER_REGISTRY}/${APP_NAME}:latest'"
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo "🧪 [Staging] Deploying to ${STAGING_SERVER} for QA Testing..."
                sh 'echo "Deploying to staging server..." && sleep 2'
                echo '✅ Staging deployment complete. Ready for QA verification.'
            }
        }

        // 🛑 ขั้นตอนการตรวจสอบ (The Gatekeeper)
        stage('Promote to Production') {
            steps {
                script {
                    // ใช้ timeout เพื่อไม่ให้ Pipeline ค้างถาวร
                    timeout(time: 1, unit: 'HOURS') { 
                        input message: '🚀 ตรวจสอบความเรียบร้อยใน Staging แล้ว: ยืนยัน Deploy ขึ้น Production หรือไม่?', 
                              ok: 'อนุมัติการ Deploy!' 
                    }
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                echo "🚚 [Production] Deploying to ${PROD_SERVER}..."
                sh 'echo "Pushing to production server..." && sleep 2'
            }
        }
    }
    
    post {
        success {
            echo '✅ Deploy สำเร็จเรียบร้อย!'
            // เพิ่ม: ส่งการแจ้งเตือนเมื่อสำเร็จ
            sh 'echo "Notification: Deployment successful! 🚀"'
        }
        failure {
            echo '❌ Pipeline พบข้อผิดพลาด!'
            // เพิ่ม: ระบบแจ้งเตือนเมื่อพัง เพื่อให้ทีมเข้าแก้ไขได้ทันที
            sh 'echo "Notification: Deployment FAILED! Please check logs. ⚠️"'
        }
        aborted {
            echo '⚠️ Pipeline ถูกยกเลิก (อาจเพราะไม่มีคนกดอนุมัติในเวลาที่กำหนด)'
        }
    }
}
