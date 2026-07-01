Se o Java estiver instalado em outro caminho, ajuste o `jdk.home` no `build.xml`:

```xml
<property name="java.release" value="25" />
<property name="jdk.home" location="C:/Program Files/Java/jdk-25.0.2" />
```

## Banco De Dados

Importe os scripts SQL:

```text
tools/sql/login/aion_ls.sql
tools/sql/game/aion_gs_mariadb_fixed.sql
tools/sql/chat/aion_cs.sql
```

Depois registre manualmente o GameServer padrao no banco `aion_ls`.

No script deste projeto a tabela se chama `gameservers` e a coluna de IP se chama `mask`.

```sql
USE `aion_ls`;

INSERT INTO `gameservers` (`id`, `mask`, `password`)
VALUES (1, '127.0.0.1', '1234');
```

Se o ID `1` ja existir, atualize o registro:

```sql
UPDATE `gameservers`
SET `mask` = '127.0.0.1',
    `password` = '1234'
WHERE `id` = 1;
```

Esse registro precisa bater com a configuracao padrao do GameServer:

```properties
gameserver.network.login.gsid = 1
gameserver.network.login.password = 1234
gameserver.network.login.address = localhost:9014
```

## Configuracoes

Confira usuario, senha e URL dos bancos nos arquivos:

```text
login-server/config/network/database.properties
game-server/config/network/database.properties
chat-server/config/network/database.properties
```

Por padrao, os bancos esperados sao:

```text
aion_ls
aion_gs
aion_cs
```

## Compilar

Pelo Ant:

```bat
ant -f build.xml dist-local
```

Esse comando compila o projeto e sincroniza os jars gerados em:

```text
game-server/libs/
login-server/libs/
```

## Empacotar

Para compilar e gerar o pacote completo:

```bat
ant -f package.xml package
```

Para apenas empacotar os arquivos ja existentes:

```bat
ant -f package.xml package-existing
```

O pacote final sera criado em:

```text
dist/aion-server.zip
```

Observacao importante sobre o `package.xml`: o ZIP final nao deve carregar uma pasta `libs` solta na raiz. As bibliotecas necessarias ficam dentro de `game-server/libs` e `login-server/libs`, que sao as pastas usadas pelos scripts de inicializacao.

## Iniciar
Ordem recomendada:

```bat
cd login-server
.\start.bat

cd ..\chat-server
.\start.bat

cd ..\game-server
.\start.bat
```

## Checklist Rapido

- Java 25 instalado e configurado.
- Projeto importado no Eclipse IDE 2022.
- Bancos `aion_ls`, `aion_gs` e `aion_cs` importados.
- Registro em `aion_ls.gameservers`: ID `1`, IP `127.0.0.1`, PASS `1234`.
- Jars gerados e sincronizados em `game-server/libs` e `login-server/libs`.
- Login Server iniciado antes do Game Server.