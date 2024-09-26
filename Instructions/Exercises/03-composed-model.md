---
lab:
  title: Criar um modelo composto de Informação de Documentos
  module: Module 6 - Document Intelligence
---

# Criar um modelo composto de Informação de Documentos

Neste exercício, você criará e treinará dois modelos personalizados que analisam diferentes formulários fiscais. Depois, você criará um modelo composto incluindo os dois modelos personalizados. Você testará o modelo enviando um formulário e verificará se ele reconhece corretamente o tipo de documento e os campos rotulados.

## Configurar os recursos

Usaremos um script para criar o recurso da IA do Azure para Informação de Documentos, uma conta de armazenamento com amostras de formulários e um grupo de recursos:

1. Inicie o Visual Studio Code.
1. Abra a paleta (SHIFT+CTRL+P) e execute o comando **Git: Clone** para clonar o repositório `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` em uma pasta local (não importa qual pasta).
1. Depois que o repositório for clonado, abra a pasta no Visual Studio Code.

    > **Observação**: Se o Visual Studio Code mostrar uma mensagem pop-up para solicitar que você confie no código que está abrindo, clique na opção **Sim, confio nos autores** no pop-up.
    
    > **Observação**: se você for solicitado a adicionar ativos necessários para compilar e depurar, selecione **Agora não**. Se houver outros pop-ups do Visual Studio Code, você poderá descartá-los com segurança.

1. Expanda a pasta **Labfiles** no painel esquerdo e clique com o botão direito do mouse no diretório **03-composed-model**. Selecione a opção para abrir no terminal integrado e execute o script a seguir:

    ```powershell
    az login --output none
    ```

    > **Observação**: Se você receber um erro sobre nenhuma assinatura ativa e tiver a MFA habilitada, talvez seja necessário fazer logon no portal do Microsoft Azure em `https://portal.azure.com` primeiro e, em seguida, executar novamente o `az login`.

1. Quando solicitado, entre em sua assinatura do Azure. Em seguida, retorne para o Visual Studio Code e aguarde a conclusão do processo de entrada.
1. No terminal integrado, execute o seguinte comando para configurar os recursos:

   ```powershell
   ./setup.ps1
   ```

   > **IMPORTANTE**: O último recurso criado pelo script é o serviço de IA do Azure para Informação de Documentos. Se esse comando falhar devido a você já ter um recurso de camada F0, use esse recurso para este laboratório ou crie um manualmente usando a camada S0 no portal do Azure.

## Criar o modelo personalizado 1040 Forms

Para criar um modelo composto, primeiro precisamos criar dois ou mais modelos personalizados. Para criar o primeiro modelo personalizado:

1. Em uma nova guia do navegador, inicie o **Estúdio de IA do Azure para Informação de Documentos** em `https://documentintelligence.ai.azure.com/studio`
1. Role a tela para baixo e, em **Modelos personalizados**, selecione **Modelo de extração personalizado**.
1. Se for solicitado a entrar na sua conta, use suas credenciais do Azure.
1. Se for perguntado qual recurso da IA do Azure para Informação de Documentos você quer usar, selecione a assinatura e o nome do recurso que você usou quando criou o recurso da IA do Azure para Informação de Documentos.
1. Em **Meus Projetos**, selecione **+ Criar um projeto**.
1. Na caixa de texto **Nome do projeto**, digite **1040 Forms** e selecione **Continuar**.
1. Na página **Configurar recurso de serviço**, na lista suspensa **Assinatura**, selecione sua assinatura do Azure.
1. Na lista suspensa **Grupo de recursos**, selecione o **DocumentIntelligenceResources&lt;xxxx&gt;** criado para você.
1. Na lista suspensa **Informação de Documentos ou Recurso do Serviço de IA do Azure**, selecione **DocumentIntelligence&lt;xxxx&gt;**.
1. Na lista suspensa **versão da API**, confirme se **31-07-2024 (Versão prévia)** está selecionado e clique em **Continuar**.
1. Na página **Conectar fonte de dados de treinamento**, na lista suspensa **Assinatura**, selecione sua assinatura do Azure.
1. Na lista suspensa **Grupo de recursos**, selecione **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Na lista suspensa **Conta de armazenamento**, selecione a única conta de armazenamento listada. Se você tiver várias contas de armazenamento na sua assinatura, escolha a que começa com *docintelstorage*
1. Na lista suspensa **Contêiner de blob**, selecione **1040examples** e clique em **Continuar**.
1. Na página **Examinar e criar** , selecione **Criar projeto**.
1. Clique em **Executar agora** no *layout Executar* na janela pop-up *Iniciar rotulagem agora* e aguarde a conclusão da análise.

## Rotular o modelo personalizado 1040 Forms

Agora, vamos rotular os campos nos formulários de exemplo:

1. Na página **Dados do rótulo**, no canto superior direito da página, selecione **+ Adicionar um campo** e, em seguida, **Campo**.
1. Digite **FirstName** e pressione *Enter*.
1. Selecione o documento chamado **f1040_1.pdf** na lista à esquerda, selecione **João** e, em seguida, selecione **FirstName**.
1. No canto superior direito da página, selecione **+ Adicionar um campo** e depois **Campo**.
1. Digite **LastName** e pressione *Enter*.
1. No documento, selecione **Doe** e depois **LastName**.
1. No canto superior direito da página, selecione **+ Adicionar um campo** e depois **Campo**.
1. Digite **City** e pressione *Enter*.
1. No documento, selecione **Los Angeles** e depois **City**.
1. No canto superior direito da página, selecione **+ Adicionar um campo** e depois **Campo**.
1. Digite **State** e pressione *Enter*.
1. No documento, selecione **CA** e depois **State**.
1. Repita o processo de rotulagem para os formulários restantes na lista à esquerda, usando os rótulos que você criou. Rotule os mesmos quatro campos: *FirstName*, *LastName*, *City* e *State*. Observe que um dos documentos não possui dados municipais ou estaduais.

> **IMPORTANTE**: para o propósito deste exercício, estamos usando apenas cinco formulários de exemplo e rotulando apenas quatro campos. Nos modelos do mundo real, você deve usar o máximo de exemplos possível para maximizar a precisão e a confiança das previsões. Você também deve rotular todos os campos disponíveis nos formulários, em vez de apenas quatro campos.

## Treinar o modelo personalizado 1040 Forms

Agora que os formulários de exemplo estão rotulados, podemos treinar o primeiro modelo personalizado:

1. Na IA do Azure para Informação de Documentos, na parte superior direita da tela, selecione **Treinar**.
1. Na caixa de diálogo **Treinar um novo modelo**, na caixa de texto **ID do Modelo**, digite **1040FormsModel**.
1. Na lista suspensa **Modo de build**, selecione **Modelo** e depois **Treinar**. 
1. Na caixa de diálogo **Treinamento em andamento**, selecione **Acessar modelos**.

## Criar o modelo personalizado 1099 Forms

Agora, você precisa criar um segundo modelo, que você vai treinar para os exemplos de formulários de imposto 1099:

1. No Estúdio de IA do Azure para Informação de Documentos, selecione **Modelo de extração personalizado**.
1. Em **Meus Projetos**, selecione **+ Criar um projeto**.
1. Na caixa de texto **Nome do projeto**, digite **1099 Forms** e selecione **Continuar**.
1. Na página **Configurar recurso de serviço**, na lista suspensa **Assinatura**, selecione sua assinatura do Azure.
1. Na lista suspensa **Grupo de recursos**, selecione **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Na lista suspensa **Informação de Documentos ou Recurso do Serviço de IA do Azure**, selecione **DocumentIntelligence&lt;xxxx&gt;**.
1. Na lista suspensa **versão da API**, confirme se **31-07-2024 (Versão prévia)** está selecionado e clique em **Continuar**.
1. Na página **Conectar fonte de dados de treinamento**, na lista suspensa **Assinatura**, selecione sua assinatura do Azure.
1. Na lista suspensa **Grupo de recursos**, selecione **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Na lista suspensa **Conta de armazenamento**, selecione a única conta de armazenamento listada.
1. Na lista suspensa **Contêiner de blob**, selecione **1099examples** e clique em **Continuar**.
1. Na página **Examinar e criar** , selecione **Criar projeto**.
1. Clique no botão suspenso de **Executar layout** e selecione **Documentos não analisados**.
1. Aguarde a conclusão da análise.

## Rotular o modelo personalizado 1099 Forms

Agora, rotule os formulários de exemplo com alguns campos:

1. Na página **Dados do rótulo**, no canto superior direito da página, selecione **+ Adicionar um campo** e, em seguida, **Campo**.
1. Digite **FirstName** e pressione *Enter*.
1. Selecione o documento chamado **f1099msc_payer.pdf**, selecione **João** e, em seguida, selecione **FirstName**.
1. No canto superior direito da página, selecione **+ Adicionar um campo** e depois **Campo**.
1. Digite **LastName** e pressione *Enter*.
1. No documento, selecione **Doe** e depois **LastName**.
1. No canto superior direito da página, selecione **+ Adicionar um campo** e depois **Campo**.
1. Digite **City** e pressione *Enter*.
1. No documento, selecione **New Haven** e depois **City**.
1. No canto superior direito da página, selecione **+ Adicionar um campo** e depois **Campo**.
1. Digite **State** e pressione *Enter*.
1. No documento, selecione **CT** e depois **State**.
1. Repita o processo de rotulagem para os formulários restantes na lista à esquerda. Rotule os mesmos quatro campos: *FirstName*, *LastName*, *City* e *State*. Observe que dois dos documentos não têm dados de nome para rotular.

## Treinar o modelo personalizado 1099 Forms

Agora você pode treinar o segundo modelo personalizado:

1. Na IA do Azure para Informação de Documentos, na parte superior direita, selecione **Treinar**.
1. Na caixa de diálogo **Treinar um novo modelo**, na caixa de texto **ID do Modelo**, digite **1099FormsModel**.
1. Na lista suspensa **Modo de build**, selecione **Modelo** e depois **Treinar**.
1. Na caixa de diálogo **Treinamento em andamento**, selecione **Acessar modelos**.
1. O processo de treinamento pode levar alguns minutos. Atualize o navegador até que os dois modelos exibam o status **bem-sucedido**.

## Usar o modelo

Agora que o modelo está concluído, vamos testá-lo com um formulário de exemplo:

1. Na IA do Azure para Informação de Documentos, selecione a página **Modelos** e depois **1040FormsModel**.
1. Selecione **Testar**.
1. Selecione **Procurar arquivos** e, em seguida, navegue até o local em que você clonou o repositório.
1. Selecione **03-composed-model/trainingdata/TestDoc/f1040_7.pdf** e, em seguida, **Abrir**.
1. Selecione **Executar análise**. A IA do Azure para Informação de Documento analisa o formulário usando o modelo composto.
1. O documento analisado é um exemplo do formulário fiscal 1040. Verifique a propriedade **DocType** para saber se o modelo personalizado correto foi usado. Verifique também os valores **FirstName**, **LastName**, **City** e **State** identificados pelo modelo.

## Limpar os recursos

Agora que você viu como os modelos compostos funcionam, vamos remover os recursos que foram criados na assinatura do Azure.

1. No **portal do Microsoft Azure** em `https://portal.azure.com/`, selecione **Grupos de recursos**.
1. Na lista de **Grupos de recursos**, selecione o **DocumentIntelligenceResources&lt;xxxx&gt;** que você criou e, em seguida, selecione **Excluir grupo de recursos**.
1. Na caixa de texto **DIGITE O NOME DO GRUPO DE RECURSOS**, digite o nome do grupo de recursos e selecione **Excluir** para excluir o recurso Informação de Documentos e a conta de armazenamento.

## Saiba mais

- [Compor modelos personalizados](/azure/ai-services/document-intelligence/concept-composed-models)
