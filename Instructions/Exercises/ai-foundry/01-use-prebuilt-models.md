---
lab:
  title: Usar modelos predefinidos da Informação de Documentos
  module: Module 6 - Document Intelligence
---

# Usar modelos predefinidos da Informação de Documentos

Neste exercício, você configurará um recurso da IA do Azure para Informação de Documentos em sua assinatura do Azure. Você usará o Estúdio da IA do Azure para Informação de Documentos e o código C# ou Python para enviar formulários para esse recurso para análise.

## Crie um recurso da IA do Azure para Informação de Documentos

Antes de chamar o serviço da IA do Azure para Informação de Documentos, você precisa criar um recurso para hospedar esse serviço no Azure:

1. Em uma guia do navegador, abra o portal do Azure em [https://portal.azure.com](https://portal.azure.com?azure-portal=true) e entre usando a conta Microsoft associada à sua assinatura do Azure.
1. Na página inicial do portal do Azure, navegue até a caixa de pesquisa superior e digite **Informação de Documentos** e pressione **Enter**.
1. Na página **Informação de Documentos**, selecione **Criar Informação de Documentos**.
1. Na página **Criar Inteligência de Documentos**, use o seguinte para configurar seu recurso:
    - **Assinatura**: Sua assinatura do Azure.
    - **Grupo de recursos**: selecione ou crie um grupo de recursos com um nome exclusivo, como *DocIntelligenceResources*.
    - **Região**: selecione uma região perto de você.
    - **Nome**: insira um nome globalmente exclusivo.
    - **Tipo de preço**: selecione **F0 Gratuito** (se você não tiver uma camada gratuita disponível, selecione **Standard S0**).
1. Selecione **Revisar + Criar** e depois **Criar**. Espere o Azure criar o recurso de IA do Azure para Informação de Documentos.
1. Depois que a implantação estiver concluída, selecione **Ir para o recurso**. Mantenha essa página aberta pelo restante deste exercício.

## Usar o modelo de Leitura

Vamos começar usando o **Estúdio da IA do Azure para Informação de Documentos** e o Modelo de leitura para analisar um documento com várias linguagens. Você conectará o Estúdio da IA do Azure para Informação de Documentos ao recurso que acabou de criar para realizar a análise:

1. Abra uma nova guia do navegador e acesse o **Estúdio da IA do Azure para Informação de Documentos** em [https://documentintelligence.ai.azure.com/studio](https://documentintelligence.ai.azure.com/studio).
1. Em **Análise de Documentos**, selecione o bloco **Leitura**.
1. Se for solicitado a entrar em sua conta, use suas credenciais do Azure.
1. Se o prompt perguntar qual recurso da IA do Azure para Informação de Documentos você quer usar, selecione a assinatura e o nome do recurso que você usou ao criar o recurso da IA do Azure para Informação de Documentos.
1. Na lista de documentos à esquerda, selecione **read-german.png**.

    ![Captura de tela mostrando a página de leitura no Estúdio da IA do Azure para Informação de Documentos.](../media/read-german-sample.png#lightbox)

1. No canto superior esquerdo, selecione **Analisar opções** e habilite a caixa de seleção **Idioma** ( em **Detecção opcional**) no painel **Analisar opções** e clique em **Salvar**. 
1. No canto superior esquerdo, selecione **Executar análise**.
1. Quando a análise é concluída, o texto extraído da imagem é mostrado à direita na guia **Conteúdo**. Examine esse texto e compare-o com o texto na imagem original para ver a precisão.
1. Selecione a guia **Resultado**. Essa guia exibe o código JSON extraído. 
1. Role até a parte inferior do código JSON na guia **Resultado**. Observe que o modelo de leitura detectou o idioma de cada trecho indicado pela `locale`. A maioria dos intervalos está em alemão (código de idioma `de`), mas você pode encontrar outros códigos de idioma nos intervalos (por exemplo, inglês - código de idioma`en` - em um dos últimos intervalos).

    ![Captura de tela que mostra a detecção do idioma para dois intervalos nos resultados do modelo de leitura no Estúdio da IA do Azure para Informação de Documentos.](../media/language-detection.png#lightbox)

## Preparar para desenvolver um aplicativo no Visual Studio Code

Agora vamos explorar o aplicativo que usa o SDK do serviço Informação de Documentos do Azure. Você desenvolverá seu aplicativo usando o Visual Studio Code. Os arquivos de código para seu aplicativo foram fornecidos em um repositório do GitHub.

> **Dica**: se você já clonou o repositório **mslearn-ai-document-intelligence**, abra-o no Visual Studio Code. Caso contrário, siga estas etapas para cloná-lo em seu ambiente de desenvolvimento.

1. Inicie o Visual Studio Code.
1. Abra a paleta (SHIFT+CTRL+P) e execute o comando **Git: Clone** para clonar o repositório `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` em uma pasta local (não importa qual pasta).
1. Depois que o repositório for clonado, abra a pasta no Visual Studio Code.

    > **Observação**: Se o Visual Studio Code mostrar uma mensagem pop-up para solicitar que você confie no código que está abrindo, clique na opção **Sim, confio nos autores** no pop-up.

1. Aguarde enquanto os arquivos adicionais são instalados para dar suporte aos projetos de código C# no repositório.

    > **Observação**: se você for solicitado a adicionar ativos necessários para compilar e depurar, selecione **Agora não**. Se aparecer a mensagem *Um projeto de função do Azure foi detectado na pasta*, pode fechar essa mensagem.

## Configurar seu aplicativo

Aplicativos para C# e Python foram fornecidos, bem como um arquivo pdf de exemplo que você usará para testar a Informações de Documentos. Ambos os aplicativos apresentam a mesma funcionalidade. Primeiro, você concluirá algumas partes importantes do aplicativo para habilitar o uso do recurso Informação de Documentos.

1. Examine a fatura a seguir e observe alguns dos campos e valores. Esse é a fatura que o código analisará.

    ![Captura de tela mostrando um documento de fatura de exemplo.](../media/sample-invoice.png#lightbox)

1. No Visual Studio Code, no painel **Explorer**, navegue até a pasta **Labfiles/01-prebuild-models** e expanda a pasta **CSharp** ou **Python** dependendo da sua preferência de linguagem. Cada pasta contém os arquivos específicos de linguagem de um aplicativo no qual você integrará a funcionalidade Informação de Documentos do Azure.

1. Clique com o botão direito do mouse na pasta **CSharp** ou **Python** que contém seus arquivos de código e selecione **Abrir um terminal integrado**. Em seguida, instale o pacote SDK do Reconhecimento de Formulários do Azure (o nome antigo para Informação de Documentos) executando o comando apropriado para sua preferência de linguagem:

    **C#**:

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**:

    ```powershell
    pip install azure-ai-formrecognizer==3.3.3
    ```

## Adicionar código para usar o serviço de Informação de Documentos do Azure

Agora você pode usar o SDK para avaliar o arquivo pdf.

1. Mude para a guia do navegador que exibe a visão geral da IA do Azure para Informação de Documentos no portal do Azure. No painel à esquerda, em *Gerenciamento de recursos*, selecione **Chaves e Ponto de Extremidade**. À direita do valor do **Ponto de extremidade**, clique no botão **Copiar para área de transferência**.
1. No painel **Explorer**, na pasta **CSharp** ou **Python**, abra o arquivo de código da linguagem de sua preferência e substitua `<Endpoint URL>` pela cadeia de caracteres que você acabou de copiar:

    **C#**: ***Program.cs***

    ```csharp
    string endpoint = "<Endpoint URL>";
    ```

    **Python**: ***document-analysis.py***

    ```python
    endpoint = "<Endpoint URL>"
    ```

1. Mude para a guia do navegador que exibe as **Chaves e Ponto de extremidade** da IA do Azure para Informação de Documentos no portal do Azure. À direita do valor de **CHAVE 1**, clique no botão *Copiar para área de transferência**.
1. No arquivo de código no Visual Studio Code, localize essa linha e substitua `<API Key>` pela cadeia de caracteres que você acabou de copiar:

    **C#**

    ```csharp
    string apiKey = "<API Key>";
    ```

    **Python**

    ```python
    key = "<API Key>"
    ```

1. Localize o comentário `Create the client`. Depois disso, em novas linhas, insira o seguinte código:

    **C#**

    ```csharp
    var cred = new AzureKeyCredential(apiKey);
    var client = new DocumentAnalysisClient(new Uri(endpoint), cred);
    ```

    **Python**

    ```python
    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )
    ```

1. Localize o comentário `Analyze the invoice`. Depois disso, em novas linhas, insira o seguinte código:

    **C#**

    ```csharp
    AnalyzeDocumentOperation operation = await client.AnalyzeDocumentFromUriAsync(WaitUntil.Completed, "prebuilt-invoice", fileUri);
    ```

    **Python**

    ```python
    poller = document_analysis_client.begin_analyze_document_from_url(
        fileModelId, fileUri, locale=fileLocale
    )
    ```

1. Localize o comentário `Display invoice information to the user`. Depois disso, em novas linhas, insira o seguinte código:

    **C#**

    ```csharp
    AnalyzeResult result = operation.Value;
    
    foreach (AnalyzedDocument invoice in result.Documents)
    {
        if (invoice.Fields.TryGetValue("VendorName", out DocumentField? vendorNameField))
        {
            if (vendorNameField.FieldType == DocumentFieldType.String)
            {
                string vendorName = vendorNameField.Value.AsString();
                Console.WriteLine($"Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}.");
            }
        }
    ```

    **Python**

    ```python
    receipts = poller.result()
    
    for idx, receipt in enumerate(receipts.documents):
    
        vendor_name = receipt.fields.get("VendorName")
        if vendor_name:
            print(f"\nVendor Name: {vendor_name.value}, with confidence {vendor_name.confidence}.")
    ```

    > [!NOTE]
    > Você adicionou código para exibir o nome do fornecedor. O projeto inicial também inclui código para exibir o *nome do cliente* e o *total da fatura*.

1. Salve as alterações no arquivo de código.

1. No painel do terminal interativo, verifique se o contexto da pasta é a pasta da sua linguagem de preferência. Em seguida, insira o comando a seguir para executar o aplicativo.

1. ***Apenas para C#***, para compilar o projeto, insira este comando:

    **C#**:

    ```powershell
    dotnet build
    ```

1. Para executar o código, insira este comando:

    **C#**:

    ```powershell
    dotnet run
    ```

    **Python**:

    ```powershell
    python document-analysis.py
    ```

O programa exibe o nome do fornecedor, o nome do cliente e o total da fatura com os níveis de confiança. Compare os valores relatados com a fatura de exemplo que você abriu no início desta seção.

## Limpeza

Quando terminar de usar o recurso do Azure, lembre-se de excluir o recurso no [portal do Azure](https://portal.azure.com/?azure-portal=true) para evitar mais cobranças.

## Mais informações

Para obter mais informações sobre o serviço Informação de Documentos, consulte a [documentação de Informação de Documentos](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true).
