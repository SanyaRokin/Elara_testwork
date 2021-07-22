# Elara_testwork
1.На хосте заходим по Root./p
2. Клонируем репозиторий:
  git clone https://github.com/SanyaRokin/Elara_testwork.git
3. Выполняем команды:
  cd ./Elara_testwork/
  chmod u+x start.sh
  sh start.sh
4. Во время работы скрипта start.sh необходимо будет создать ключи ssh и ввести credention для aws.
5. сервер git поднимается самостоятельно, за это отвечает terraform:
    provisioner "remote-exec" {
    inline = [
      "sudo apt update && sudo apt upgrade -y",
      "sudo apt-get install git-core -y",
      "mkdir -p /tmp/project-1.git && cd /tmp/project-1.git",
      "git init --bare"
    ]
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = "${file("/tmp/id_rsa.pem")}"
      host        = aws_instance.agent.public_ip
    }
  }
6. Сервер Teamcity поднимается автоматически, за это отвечает terraform.
7. После выполнения скрипта необходимо передать id_rsa.pem с хоста на server(cp /tmp/id_rsa.pem ubuntu@<ip-server>:/opt/docker/teamCity/teamcity_server/datadir/). 
   ip servera, agenta и git будет результатом работы terraform в командной строке.
8. В браузере вводим:
    <ip server>:9111
    и проводим первоначальную настройку Teamcity, добавляем id_rsa.pem в список credentions.
9. Настройка Agent Teamcity:
   Переходим в Agents-Agents Push - Install agent...
   Host - <ip agent>, 
   Use custom settings
   Platform UNIX
   Authentication method:	 Private-Key
   Username:  Ubuntu
   Private key file:  путь до id_rsa.pem
   Install Agent.
10. Создаем проект и делаем сборку.
   
