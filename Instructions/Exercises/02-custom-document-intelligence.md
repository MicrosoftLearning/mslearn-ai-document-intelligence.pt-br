---
lab:
  title: Extrair dados de formulários
  module: Module 11 - Reading Text in Images and Documents
---

# Extrair dados de formulários 

Considere que uma empresa atualmente exija que os funcionários comprem manualmente folhas de pedidos e insiram os dados em um banco de dados. Eles gostariam que você utilizasse serviços de IA para melhorar o processo de entrada de dados. Você decide criar um modelo de machine learning para ler o formulário e produzir dados estruturados que podem ser usados para atualizar automaticamente um banco de dados.

**IA do Azure para Informação de Documentos** é um serviço de IA do Azure que permite aos usuários criar um software de processamento de dados automatizado. Este software pode extrair texto, pares de chave/valor e tabelas de documentos de formulário usando OCR (reconhecimento óptico de caracteres). O IA do Azure para Informações de Documentos tem modelos pré-criados para reconhecer faturas, recibos e cartões de visita. O serviço também fornece a capacidade de treinar modelos personalizados. Neste exercício, nos concentraremos na criação de modelos personalizados.

## Preparar para desenvolver um aplicativo no Visual Studio Code

Agora vamos usar o SDK do serviço para desenvolver um aplicativo usando o Visual Studio Code. Os arquivos de código para o aplicativo foram fornecidos em um repositório do GitHub.

> **Dica**: se você já clonou o repositório **mslearn-ai-document-intelligence**, abra-o no Visual Studio Code. Caso contrário, siga estas etapas para cloná-lo em seu ambiente de desenvolvimento.

1. Inicie o Visual Studio Code.
1. Abra a paleta (SHIFT+CTRL+P) e execute um comando **Git: Clone** para clonar o repositório `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` para uma pasta local (não importa qual pasta).
1. Depois que o repositório for clonado, abra a pasta no Visual Studio Code.
1. Aguarde enquanto arquivos adicionais são instalados para oferecer suporte aos projetos de código C# no repositório.

    > **Observação**: se você for solicitado a adicionar os ativos necessários para compilar e depurar, selecione **Agora não**. Se receber a mensagem *Detectado um projeto de função do Azure na pasta*, você pode fechá-la com segurança.

## Criar um recurso da IA do Azure para Informação de Documentos

Para usar o serviço IA do Azure para Informações de Documentos, você precisa de um recurso IA do Azure para Informações de Documentos ou Serviços de IA do Azure na assinatura do Azure. Você usará o portal do Azure para criar um recurso.

1. Em uma guia do navegador, abra o portal do Azure em `https://portal.azure.com` e entre usando a conta Microsoft associada à sua assinatura do Azure.
1. Na página inicial do do portal do Azure, vá até a caixa de pesquisa superior e digite **Informação de Documentos** e pressione **Enter**.
1. Na página **Informação de Documentos**, selecione **Criar**.
1. Na página **Criar inteligência de documento**, use o seguinte para configurar o recurso:
    - **Assinatura**: Sua assinatura do Azure.
    - **Grupo de recursos**: selecione ou crie um grupo de recursos com um nome exclusivo, como *DocIntelligenceResources*.
    - **Região**: selecione uma região perto de você.
    - **Nome**: insira um nome globalmente exclusivo.
    - **Tipo de preço**: selecione **F0 gratuito** (se você não tiver uma camada gratuita disponível, selecione **S0 padrão**).
1. Selecione **Examinar + Criar** e depois **Criar**. Espere o Azure criar o recurso de IA do Azure para Informação de Documentos.
1. Depois que a implantação for concluída, selecione **Ir para o recurso** para exibir a página **Visão geral** do recurso. 

## Reunir documentos para treinamento

Você usará os formulários de exemplo como esse para treinar um modelo de teste: 

![Uma imagem de uma fatura usada neste projeto.](../media/Form_1.jpg)

1. Volte para o **Visual Studio Code**. No painel *Explorer*, abra a pasta **Labfiles/02-custom-document-intelligence** e expanda a pasta **sample-forms**. Há arquivos terminados em **.json** e **.jpg** na pasta.

    Você usará os arquivos **.jpg** para treinar o modelo.  

    Os arquivos **.json** foram gerados para você e contêm informações de rótulo. Os arquivos serão carregados no contêiner de armazenamento de blobs juntamente com os formulários.

    Você pode exibir as imagens usadas na pasta *sample-forms* de exemplo selecionando-as no Visual Studio Code.

1. Volte para o **portal do Azure** e vá até a página **Visão geral** do seu recurso, se ainda não estiver lá. Na seção *Essentials*, veja o **Grupo de recursos** no qual você criou o recurso de inteligência do documento.

1. Na página **Visão geral** do seu grupo de recursos, observe a **ID da assinatura** e o **Local**. Você precisará desses valores, juntamente com o nome do **grupo de recursos** nas etapas subsequentes.

    ![Um exemplo da página de grupo de recursos.](../media/resource_group_variables.png)

1. No Visual Studio Code, no painel *Explorer*, navegue até a pasta **Labfiles/02-custom-document-intelligence** e expanda a pasta **CSharp** ou **Python** dependendo da sua preferência de idioma. Cada pasta contém os arquivos específicos de idioma de um aplicativo no qual você integrará a funcionalidade do Azure OpenAI.

1. Clique com o botão direito do mouse na pasta **CSharp** ou **Python** que contenha seus arquivos de código e selecione **Abrir um terminal integrado**. 

1. No terminal, execute o seguinte comando para fazer logon no Azure. O comando **az login** abre o navegador Microsoft Edge, faça logon com a mesma conta que você usou para criar o recurso de IA do Azure para Informação de Documentos. Depois de conectar-se, feche a janela do navegador.

    ```powershell
    az login
    ```

1. Retorne ao Visual Studio Code. No painel do terminal, execute o seguinte comando para listar os locais do Azure.

    ```powershell
    az account list-locations -o table
    ```

1. Na saída, localize o valor **Name** que corresponde ao local do seu grupo de recursos (por exemplo, para *Leste dos EUA* o nome correspondente é *eastus*).

    > **Importante**: guarde o valor **Name** e use-o na Etapa 11.

1. No Visual Studio Code, na pasta **Labfiles/02-custom-document-intelligence**, selecione **setup.cmd**. Você usará esse script para executar os comandos da CLI (interface de linha de comando) do Azure necessários para criar os outros recursos do Azure necessários.

1. No script **setup.cmd**, revise os comandos. O programa irá:
    - Criar uma conta de armazenamento no seu grupo de recursos do Azure
    - Carregar arquivos da pasta local *sampleforms* para um contêiner chamado *sampleforms* na conta de armazenamento
    - Imprimir um URI de Assinatura de Acesso Compartilhado

1. Modificar as declarações das variáveis **subscription_id**, **resource_group** e **location** com os valores apropriados para a assinatura, o grupo de recursos e o nome do local onde você implantou o recurso de Informação de Documentos.
Em seguida, **salve** as alterações.

    Deixe a variável **expiry_date** como está para o exercício. Essa variável é usada ao gerar o URI de SAS (Assinatura de Acesso Compartilhado). Na prática, você vai querer definir uma data de validade apropriada para a sua SAS. Você pode aprender mais sobre a SAS [aqui](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works).  

1. No terminal da pasta **Labfiles/02-custom-document-intelligence**, digite o seguinte comando para executar o script:

    ```PowerShell
    $currentdir=(Get-Item .).FullName
    cd ..
    ./setup.cmd
    cd $currentdir

    ```

1. Quando o script for concluído, revise a saída exibida.

1. No portal do Azure, atualize seu grupo de recursos e verifique se ele contém a conta de armazenamento do Azure recém-criada. No painel à esquerda do painel da conta de armazenamento, selecione **Navegador de armazenamento**. Em seguida, em Navegador de Armazenamento, expanda **Contêineres de Blob** e selecione o contêiner **sampleforms** para verificar se os arquivos foram carregados da pasta local **02-custom-document-intelligence/sample-forms**.

## Utilize o Estúdio de Informação de Documentos para treinar o modelo

Agora você treinará o modelo usando os arquivos carregados na conta de armazenamento.

1. No navegador, vá para o Estúdio de Informação de Documentos em `https://documentintelligence.ai.azure.com/studio`.
1. Role para baixo até a seção **Modelos personalizados** e selecione o bloco de **Modelo de extração personalizado**.
1. Se for solicitado a entrar em sua conta, use suas credenciais do Azure.
1. Se o prompt perguntar qual recurso da IA do Azure para Informação de Documentos você quer usar, selecione a assinatura e o nome do recurso que você usou ao criar o recurso da IA do Azure para Informação de Documentos.
1. Em **Meus projetos**, selecione **Criar um projeto**. Use as seguintes configurações:

    - **Nome do projeto**: insira um nome exclusivo.
        - Selecione *Continuar*.
    - **Configurar recurso de serviço**: selecione a assinatura, o grupo de recursos e o recurso de inteligência de documentos que você criou anteriormente neste laboratório. Marque a caixa de seleção *Definir como padrão*. Mantenha a versão de API padrão. 
        - Selecione *Continuar*.
    - **Conectar fonte de dados de treinamento**: selecione a assinatura, o grupo de recursos e a conta de armazenamento criados pelo script de instalação. Marque a caixa de seleção *Definir como padrão*. Selecione o contêiner de blob `sampleforms` e deixe o caminho da pasta em branco.
        - Selecione *Continuar*.
    - Selecione *Criar projeto*

1. Depois de criar o projeto, selecione **Treinar** para treinar seu modelo. Use as seguintes configurações:
    - **ID do modelo**: *forneça um nome globalmente exclusivo (você precisará do nome do ID do modelo na próxima etapa)*. 
    - **Modo de criação**: modelo.
1. O treinamento pode levar algum tempo. Você receberá uma notificação quando ele for concluído.

## Teste o seu modelo personalizado de Informação de documentos

1. Retorne ao Visual Studio Code. No terminal, instale o pacote de Informação de Documentos executando o comando apropriado para sua preferência de idioma:

    **C#**:

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**:

    ```powershell
    pip install azure-ai-formrecognizer==3.3.0
    ```

1. No Visual Studio Code, na pasta **Labfiles/02-custom-document-intelligence**, selecione o idioma que você está usando. Edite o arquivo de configuração (**appsettings.json** ou **.env**, dependendo da sua preferência de idioma) com os seguintes valores:
    - Seu ponto de extremidade de Informação de Documentos.
    - Sua chave da Informação de Documentos.
    - A ID do modelo que você informou ao treinar seu modelo. Você pode encontrar isso na página **Modelos** do Estúdio de Informação de Documentos. **Salve** as alterações.

1. No Visual Studio Code, abra o arquivo de código para seu aplicativo cliente (*Program.cs* para C#, *test-model.py* para Python) e revise o código contido nele, especialmente que a imagem na URL se refere ao arquivo neste repositório do GitHub na Web.

1. Volte para o terminal e insira o seguinte comando para executar o programa:

    **C#**

    ```powershell
    dotnet build
    dotnet run
    ```

    **Python**

    ```powershell
    python test-model.py
    ```

1. Exiba a saída e observe como a saída para o modelo fornece nomes de campo como `Merchant` e `CompanyPhoneNumber`.

## Limpar

Quando terminar de usar o recurso do Azure, lembre-se de excluir o recurso no [portal do Azure](https://portal.azure.com/?azure-portal=true) para evitar mais cobranças.

## Mais informações

Para obter mais informações sobre o serviço de Informação de Documentos, consulte a [documentação da Informação de Documentos](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true).
