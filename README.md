# gradle-mod-upload-task
Показан код для build.gradle для быстрой загрузки мода прямо на сервер, дальше нужно обновить мод, можно автоматически это делать при загрузке мода с режимом отладки в animation-api (Kit.DEBUG) 

```groovy
plugins {
    id 'org.hidetake.ssh' version '2.10.1' // Добавляем нужный плагин для создания ssh-клиента
}

apply plugin: 'org.hidetake.ssh' // Выполняем этот плагин

remotes {
    webServer {
        host = 'some.host.com' // Указываем хост
        user = 'func' // Имя пользователя
        knownHosts = allowAnyHosts // Разрешаем поключаться к любым серверам
        identity = file('C://Users/func/.ssh/private.ppk') // Файл с приватным ключом
        passphrase = "пароль от приватного ключа если есть"
    }
}

task upload() { 
    doLast {
        ssh.run {
            session(remotes.webServer) {
                // Берем проект в gradle :mod и берем результат jar таски, затем меняем -raw на -bundle и получаем короткую версию мода и кидаем в папку на сервере
                put from: project(':mod').tasks.jar.getArchiveFile().get().asFile.path.replace("-raw", "-bundle"), into: "/home/func/forest_new/realms/TWR-1/mods"
            }
        }
    }
}

afterEvaluate {
    tasks.upload.dependsOn(tasks.bundle) // Делаем так, чтобы при таске upload, он перед этим собирал мод
}
```
