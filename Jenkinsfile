pipeline {
    agent any
    
    stages {
        stage('Build & Test') {
            steps {
                echo '🛠️ Building and testing the application...'
                sh 'echo "Running tests..." && sleep 2' 
            }
        }

        // 🛑 ขั้นตอนการตรวจสอบ (The Gatekeeper)
        stage('Promote to Production') {
            steps {
                script {
                    // ใช้ timeout เพื่อไม่ให้ Pipeline ค้างถาวรหากไม่มีคนมากดอนุมัติภายใน 1 ชม.
                    timeout(time: 1, unit: 'HOURS') { 
                        input message: '🚀 ตรวจสอบความเรียบร้อย: คุณยืนยันที่จะ Deploy งานนี้ขึ้น Production หรือไม่?', 
                              ok: 'อนุมัติการ Deploy!' 
                    }
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                echo '🚚 Deploying to Production Server...'
                sh 'echo "Pushing to prod..." && sleep 2'
            }
        }
    }
    
    post {
        aborted {
            echo '⚠️ Pipeline ถูกยกเลิก (อาจเพราะไม่มีคนกดอนุมัติในเวลาที่กำหนด)'
        }
        success {
            echo '✅ Deploy สำเร็จเรียบร้อย!'
        }
    }
}