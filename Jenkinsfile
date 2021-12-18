pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('DeployToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    key: "-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAm2sT0o+x1/vN4ST1PoNBdjhI9TkXNJy9ee7hi94uRrRRzznC
vw5OjOTEh65ZmOx12JXG6+/rKnSwOWSc3Yfns5ZW4SPOw2iPkRdtMciI59E5SR6B
Xwgk/Xh2tFc8k6Vpp8+37LvmIB2pAI/NV5GE03npE++Pj6QKdb6o89xMHoYjd/bC
4uakfTjS0/mqeQyEVwCmiLAez6DRBqtdBDLMPsgtt4oDtlht17EvUXDuDaNrkbFp
7bPLq7r11nYf2IPo5p4mIdxu2IZfIm1+gb6GAQQbbTvKKj0QY7W/qLzs35YHC25V
n/ehjPYHfm0ic9RoT3ZbFYhwdHRmVAn01myeTQIDAQABAoIBADcFx7TKDqpqiU7m
KLUC/w/9iUbX+XE09JfQjKVZ+YQqnrQAvQOq6wBSlYl7nljXjjkpfY1WGSmkuib3
idmcHdmkpgl7Bd6EjB9P7easGlwH5IIQfLjKR149YbkjEV0sOKWiknkLZMOF/Kko
QAh6ik3cYTofm/G8n79jyqjbG0/Kmm8X7z7tcEIBODoCkhlmYwVod66GTt9eah/d
mq+ilufVfAxKpVmXoAQbcdD4v7J0GERvZ8+2oY3nimVCGJA+/kgVynvR4JjfhlFP
jcx99y+A59LciHidoEcCi0sErXBG3pKiyO4WZRmELAOsxGWWK/wdndT/p/yvzzsf
kkrRyfkCgYEA6uZk125JYQaFx1+VDZCbtWHrGj89eqgxXDMNvq+8MA8+jtoRtxZF
SbMDWGTE3PtmoQvBslZ55Fd4ZYlbZcP+e37VmyX3vbU/DJOZJwMhbdB3NXlcAJD5
KifL5iEBJCf8TnGbibabvR3VyqBGlpZnP91/kADzlyXfl0MwwWgcNYcCgYEAqWD5
aRiLAEDn2UK2FVQIx5bXZSarlLXuJiHj+/gISnd4zohOMwZd7kfQ1rinYY8BxpyJ
Y7OT1cICbviarHYEtFEp8M5BMUXg38F/Md6m8KEXZghN6NU+EFS61/I6wL2OIGNC
N8mtl/NcHGMB+v2jVl0SKDJVX4PaebjhWZ/rgosCgYEA1yLyItjQffuSVNq20fiq
k4I96AkZjpgytQXRRd7/1Whw3DbfWLcd+HTq0G9iWh4qjb8NxP8snjR6gUw/GEJc
ddDhZDpu3yH7v9ICx9c3K7NtKt2epKoiejh+p6N8vxecHmAwmv1TP+7uWqlWXR8d
K0H7TfGBJ+K5GHmiUtC+XUsCgYEApSaN6aBpoBSpO9jlK4Sljt6QJOuAr15416uB
3WPpyZoWK+MRxIS3v6c8hLDFW8WRqLNm7tThF7K0prk2L2Rfj+X3q/mf7UhCTEoi
GGENi2C3LOB5/sU1X70N9ZMVIGtzEjdWEIlP/vHl5TPQcYcv5PyYwKLbRWeN9dOD
vtzESKMCgYEA1RzKmRH9ShNZQewAMwk92ZKszx9eoQCGXpGaokvJPM00a6dHT9Oi
o3nveXbxXOIt5ABOR0gzKwpFjmt6qvn5eLMDIOmPJJQe2uf8Rw1DDmWSBJyNDoCc
PfmX/CzgRfOTAtUDmrmjZXEvaNupl6NeEIs8IP6GCk1vqbSvTK8Zgrc=
-----END RSA PRIVATE KEY-----"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/trainSchedule.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environment look OK?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'production',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    key: "-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAm2sT0o+x1/vN4ST1PoNBdjhI9TkXNJy9ee7hi94uRrRRzznC
vw5OjOTEh65ZmOx12JXG6+/rKnSwOWSc3Yfns5ZW4SPOw2iPkRdtMciI59E5SR6B
Xwgk/Xh2tFc8k6Vpp8+37LvmIB2pAI/NV5GE03npE++Pj6QKdb6o89xMHoYjd/bC
4uakfTjS0/mqeQyEVwCmiLAez6DRBqtdBDLMPsgtt4oDtlht17EvUXDuDaNrkbFp
7bPLq7r11nYf2IPo5p4mIdxu2IZfIm1+gb6GAQQbbTvKKj0QY7W/qLzs35YHC25V
n/ehjPYHfm0ic9RoT3ZbFYhwdHRmVAn01myeTQIDAQABAoIBADcFx7TKDqpqiU7m
KLUC/w/9iUbX+XE09JfQjKVZ+YQqnrQAvQOq6wBSlYl7nljXjjkpfY1WGSmkuib3
idmcHdmkpgl7Bd6EjB9P7easGlwH5IIQfLjKR149YbkjEV0sOKWiknkLZMOF/Kko
QAh6ik3cYTofm/G8n79jyqjbG0/Kmm8X7z7tcEIBODoCkhlmYwVod66GTt9eah/d
mq+ilufVfAxKpVmXoAQbcdD4v7J0GERvZ8+2oY3nimVCGJA+/kgVynvR4JjfhlFP
jcx99y+A59LciHidoEcCi0sErXBG3pKiyO4WZRmELAOsxGWWK/wdndT/p/yvzzsf
kkrRyfkCgYEA6uZk125JYQaFx1+VDZCbtWHrGj89eqgxXDMNvq+8MA8+jtoRtxZF
SbMDWGTE3PtmoQvBslZ55Fd4ZYlbZcP+e37VmyX3vbU/DJOZJwMhbdB3NXlcAJD5
KifL5iEBJCf8TnGbibabvR3VyqBGlpZnP91/kADzlyXfl0MwwWgcNYcCgYEAqWD5
aRiLAEDn2UK2FVQIx5bXZSarlLXuJiHj+/gISnd4zohOMwZd7kfQ1rinYY8BxpyJ
Y7OT1cICbviarHYEtFEp8M5BMUXg38F/Md6m8KEXZghN6NU+EFS61/I6wL2OIGNC
N8mtl/NcHGMB+v2jVl0SKDJVX4PaebjhWZ/rgosCgYEA1yLyItjQffuSVNq20fiq
k4I96AkZjpgytQXRRd7/1Whw3DbfWLcd+HTq0G9iWh4qjb8NxP8snjR6gUw/GEJc
ddDhZDpu3yH7v9ICx9c3K7NtKt2epKoiejh+p6N8vxecHmAwmv1TP+7uWqlWXR8d
K0H7TfGBJ+K5GHmiUtC+XUsCgYEApSaN6aBpoBSpO9jlK4Sljt6QJOuAr15416uB
3WPpyZoWK+MRxIS3v6c8hLDFW8WRqLNm7tThF7K0prk2L2Rfj+X3q/mf7UhCTEoi
GGENi2C3LOB5/sU1X70N9ZMVIGtzEjdWEIlP/vHl5TPQcYcv5PyYwKLbRWeN9dOD
vtzESKMCgYEA1RzKmRH9ShNZQewAMwk92ZKszx9eoQCGXpGaokvJPM00a6dHT9Oi
o3nveXbxXOIt5ABOR0gzKwpFjmt6qvn5eLMDIOmPJJQe2uf8Rw1DDmWSBJyNDoCc
PfmX/CzgRfOTAtUDmrmjZXEvaNupl6NeEIs8IP6GCk1vqbSvTK8Zgrc=
-----END RSA PRIVATE KEY-----"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/trainSchedule.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}
