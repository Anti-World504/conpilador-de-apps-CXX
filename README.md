# CXX-APP-on-Termux
conpilado de Aplicativos Android Nativos em C++ para Termux
Um pequeno repositório que hospeda um backup do Termux para desenvolvedores que desejam criar aplicativos Android nativos usando C++ puro, sem precisar escrever código Java.
O projeto fornece um ambiente Termux pré-configurado e um sistema de compilação simples para transformar código C++ em arquivos APK usando o Android NDK e ferramentas de linha de comando.
Índice
Visão geral
Requisitos
Instalação
Restaurando o backup do Termux
Como funciona
Processo de compilação
Compilando seu aplicativo
Localização do APK gerado
Solução de problemas
Arquivos do backup
Visão geral
O objetivo deste projeto é permitir o desenvolvimento de aplicativos Android nativos em C++ diretamente no seu dispositivo Android, utilizando o Termux.
Em vez de depender de um projeto tradicional do Android Studio, esta configuração utiliza ferramentas de linha de comando para compilar o código C++, empacotar a biblioteca nativa em um APK, alinhar o pacote e assinar o aplicativo.
O repositório inclui um backup do Termux e uma pasta de exemplo chamada app, que você pode modificar para criar seus próprios aplicativos.
Requisitos
Antes de começar, certifique-se de que você possui:
Um dispositivo Android.
O Termux, preferencialmente na versão mais recente disponível no F-Droid ou no repositório oficial do GitHub.
Espaço de armazenamento suficiente para baixar e extrair os arquivos.
Conhecimentos básicos de C++, caso queira modificar o aplicativo de exemplo.
Importante: o backup possui aproximadamente 1,36 GB. Certifique-se de ter espaço livre suficiente antes de baixá-lo e restaurá-lo.
Instalação
1. Baixe do Drive
Baixe o arquivo ZIP disponibilizado aqui https://drive.google.com/drive/folders/1qLSJd4ofVE6bV-e1EvHG_hoHH-hjEmsb e extraia seu conteúdo no dispositivo.
Localize o arquivo termux-backup.tar.gz dentro da pasta extraída.
2. Mova o backup para a raiz do armazenamento interno
Mova o arquivo termux-backup.tar.gz para a raiz do armazenamento interno compartilhado do seu dispositivo.
O caminho esperado é:
/storage/emulated/0/termux-backup.tar.gz
Dependendo do gerenciador de arquivos utilizado, a raiz pode aparecer com o nome de Armazenamento interno.
A estrutura de diretórios deve ficar parecida com esta:

Armazenamento interno/
├── Android/
├── Download/
├── Music/
├── DCIM/
├── Pictures/
└── termux-backup.tar.gz

O arquivo de backup deve ficar diretamente na raiz do armazenamento interno, e não dentro da pasta Download.
Restaurando o backup do Termux
1. Instale e abra o Termux
Instale o Termux pelo F-Droid ou pelo repositório oficial do GitHub. Depois, abra o aplicativo.
Aviso: restaurar esse backup pode sobrescrever arquivos e configurações existentes do Termux. Faça uma cópia dos seus dados importantes antes de continuar.
O backup também pode apresentar problemas se for restaurado em uma versão ou instalação diferente daquela para a qual foi criado.
2. Restaure o backup
Execute o seguinte comando dentro do Termux:
tar -zxf /sdcard/termux-backup.tar.gz \
  -C /data/data/com.termux/files \
  --recursive-unlink \
  --preserve-permissions
Aguarde até que a extração seja concluída. Como o arquivo é grande, o processo pode demorar alguns minutos ou mais, dependendo do dispositivo.
Se a restauração for concluída corretamente, seu ambiente Termux pré-configurado deverá estar pronto para uso.
Como funciona
A ideia básica é simples: um APK é um pacote baseado no formato ZIP que contém o manifesto do aplicativo, o código compilado e outros recursos necessários, além de uma assinatura digital.
Este projeto utiliza o NativeActivity, um componente do Android que permite executar código C++ nativo sem precisar escrever código Java para o aplicativo.
Os principais componentes são:
AndroidManifest.xml — define a identidade do aplicativo, seu ponto de entrada e suas configurações.
main.cpp — contém o código-fonte C++ do aplicativo.
libmain.so — biblioteca nativa compilada a partir do código C++.
android_native_app_glue.c — ajuda a conectar o aplicativo nativo ao ciclo de vida do Android.
aapt — empacota os recursos do Android e adiciona arquivos ao APK.
zipalign — alinha o conteúdo do APK para atender aos requisitos de empacotamento.
apksigner — assina o APK para que o Android possa verificar sua assinatura.
O manifesto pode utilizar android:hasCode="false" quando o aplicativo não contém código Java ou DEX. Nesse caso, a biblioteca nativa é carregada por meio do NativeActivity.
Fluxo de compilação
main.cpp
   |
   | clang++ (Android NDK)
   v
libmain.so
   |
   | aapt package + aapt add
   v
app.apk
   |
   | zipalign
   v
APK alinhado
   |
   | apksigner
   v
app.apk assinado
Os comandos exatos e os arquivos intermediários dependem da implementação do build.sh.
Gradle e CMake não compilam o código sozinhos: eles coordenam as ferramentas de compilação subjacentes. Este projeto utiliza um script de shell para executar diretamente as ferramentas necessárias.
Processo de compilação
O script de compilação automatiza as etapas necessárias para transformar seu código C++ em um APK instalável.
Etapa
Ferramenta
Finalidade
1
clang++ do NDK
Compila o código C++ para a arquitetura Android desejada, como ARM64.
2
aapt package
Cria o pacote APK inicial a partir do manifesto e dos recursos.
3
aapt add
Adiciona a biblioteca nativa compilada ao APK.
4
zipalign
Alinha o conteúdo do pacote.
5
keytool e apksigner
Geram ou utilizam uma chave de assinatura e assinam o APK.
Uma chave de assinatura é necessária para assinar o APK. Durante o desenvolvimento, o projeto pode utilizar uma chave gerada localmente.
Guarde sua chave de assinatura e suas senhas em segurança caso pretenda publicar atualizações do mesmo aplicativo.
Compilando seu aplicativo
Recomendo começar pela pasta app incluída neste repositório.
Ela contém o aplicativo de exemplo e os arquivos utilizados pelo processo de compilação. Você pode modificar o código-fonte e as configurações para criar seu próprio aplicativo.
1. Modifique o projeto
Abra a pasta app e edite os arquivos de código C++ e as configurações relevantes conforme suas necessidades.
Por exemplo, você pode modificar o arquivo main.cpp para implementar a lógica do seu aplicativo.
Antes de remover ou renomear arquivos, verifique se eles são necessários para o funcionamento do script de compilação.
2. Dê permissão de execução ao script
Se aparecer o erro Permission denied ao executar o script, utilize:
chmod +x build.sh
3. Compile o aplicativo
Entre no diretório que contém o arquivo build.sh e execute:
./build.sh
O script deverá compilar o código C++, empacotar a biblioteca nativa, alinhar o APK e assinar o aplicativo.
Se a compilação terminar com sucesso, procure o APK gerado no diretório de saída indicado abaixo.
Localização do APK gerado
O APK deverá ser salvo no seguinte diretório:
/storage/emulated/0/Download/
Abra a pasta Download do seu dispositivo para localizar o arquivo do aplicativo.
Se o APK não aparecer nesse local, verifique o caminho de saída configurado dentro do build.sh e confira as mensagens exibidas durante a compilação.
Solução de problemas
Erro Permission denied
Dê permissão de execução ao script:
chmod +x build.sh
Depois, tente novamente:
./build.sh
O arquivo de backup não foi encontrado
Verifique se o arquivo está localizado em:
/storage/emulated/0/termux-backup.tar.gz
Você também pode verificar se o Termux consegue acessá-lo:
ls -lh /sdcard/termux-backup.tar.gz
Se for necessário conceder acesso ao armazenamento, execute:
termux-setup-storage
Conceda a permissão solicitada e tente novamente.
O APK não foi gerado
Verifique as mensagens do terminal em busca de erros de compilação ou empacotamento.
Confirme se as ferramentas necessárias do NDK e os utilitários de empacotamento do Android estão disponíveis e se os caminhos utilizados pelo build.sh estão corretos.
O APK não pode ser instalado
Certifique-se de que o APK foi assinado corretamente, utiliza uma arquitetura compatível com o dispositivo e possui uma configuração de pacote aceita pela versão do Android instalada.
Verifique também se as etapas de assinatura e alinhamento foram concluídas sem erros.
Arquivos do backup
O repositório inclui os seguintes arquivos relacionados ao backup:
Arquivo
Descrição
termux-backup.tar.gz
Backup compactado do Termux, com aproximadamente 1,36 GB.
backup.txt
Contém o comando utilizado para criar o backup.
backup restore.txt
Contém o comando de restauração, incluindo opções para lidar com links simbólicos e permissões de arquivos.
Esses arquivos permitem reproduzir o ambiente e entender como o backup é criado e restaurado.
Considerações finais
Este projeto foi desenvolvido como ponto de partida para quem deseja experimentar o desenvolvimento de aplicativos Android nativos em C++, diretamente de um dispositivo Android.
Explore a pasta app, modifique o código-fonte e adapte o script de compilação conforme suas necessidades.
