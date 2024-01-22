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
1. Aguarde enquanto os arquivos adicionais são instalados para dar suporte aos projetos de código C# no repositório.

    > **Observação**: se você for solicitado a adicionar os ativos necessários para compilar e depurar, selecione **Agora não**.

1. Clique com o botão direito do mouse no diretório **03-composed-model**, abra no terminal integrado e execute o script de instalação:

   ``` bash
   bash setup.sh
   ```

## Criar o modelo personalizado 1040 Forms

Para criar um modelo composto, primeiro precisamos criar dois ou mais modelos personalizados. Para criar o primeiro modelo personalizado:

1. Em uma nova guia do navegador, inicie o [Estúdio da IA do Azure para Informação de Documentos](https://formrecognizer.appliedai.azure.com/studio).
1. Role para baixo e, em **Modelo personalizado**, selecione **Modelo personalizado**.
1. Se for solicitado a entrar na sua conta, use suas credenciais do Azure.
1. Se for perguntado qual recurso da IA do Azure para Informação de Documentos você quer usar, selecione a assinatura e o nome do recurso que você usou quando criou o recurso da IA do Azure para Informação de Documentos.
1. Em **Meus Projetos**, selecione **+ Criar um projeto**.
1. Na caixa de texto **Nome do projeto**, digite **1040 Forms** e selecione **Continuar**.
1. Na página **Configurar recurso de serviço**, na lista suspensa **Assinatura**, selecione sua assinatura do Azure.
1. Na lista suspensa **Grupo de recursos**, selecione **DocumentIntelligenceResources**.
1. Na lista suspensa **IA do Azure para Informação de Documentos ou Recurso do Serviço de IA do Azure**, selecione **DocumentIntelligence**
1. Na lista suspensa **Versão da API**, verifique se **2022-06-30-preview** está selecionado e clique em **Continuar**.

    :::image type="content" source="../media/4-configure-service-resources.png" alt-text="Captura de tela mostrando a página de recursos do serviço Configurar no assistente do modelo personalizado do Estúdio da IA do Azure para Informação de Documentos." lightbox="../media/4-configure-service-resources.png":::

1. Na página **Configurar fonte de dados de treinamento**, na lista suspensa **Assinatura**, selecione sua assinatura do Azure.
1. Na lista suspensa **Grupo de recursos**, selecione **DocumentIntelligenceResources**.
1. Na lista suspensa **Conta de armazenamento**, selecione a única conta de armazenamento listada.
1. Na lista suspensa **Contêiner de blob**, selecione **1040examples** e clique em **Continuar**.

    :::image type="content" source="../media/4-connect-training-data-source.png" alt-text="Captura de tela mostrando a página da fonte de dados de treinamento do recurso Conectar no assistente do modelo personalizado do Estúdio da IA do Azure para Informação de Documentos." lightbox="../media/4-connect-training-data-source.png":::

1. Na página **Examinar e criar** , selecione **Criar projeto**.

## Rotular o modelo personalizado 1040 Forms

Agora, vamos rotular os campos nos formulários de exemplo:

1. Na página **Dados de rótulo**, no canto superior direito da página, selecione **+** e depois **Campo**.

    :::image type="content" source="../media/4-add-label.png" alt-text="Captura de tela mostrando como adicionar um novo rótulo no Estúdio da IA do Azure para Informação de Documentos." lightbox="../media/4-add-label.png":::

1. Digite **FirstName** e pressione <kbd>Enter</kbd>.
1. No documento, selecione **John** e depois **FirstName**.

    :::image type="content" source="../media/4-label-first-name.png" alt-text="Captura de tela mostrando como preencher um novo rótulo no Estúdio da IA do Azure para Informação de Documentos." lightbox="../media/4-label-first-name.png":::

1. No canto superior direito da página, selecione **+** e depois **Campo**.
1. Digite **LastName** e pressione <kbd>Enter</kbd>.
1. No documento, selecione **Doe** e depois **LastName**.
1. No canto superior direito da página, selecione **+** e depois **Campo**.
1. Digite **City** e pressione <kbd>Enter</kbd>.
1. No documento, selecione **Los Angeles** e depois **City**.
1. No canto superior direito da página, selecione **+** e depois **Campo**.
1. Digite **State** e pressione <kbd>Enter</kbd>.
1. No documento, selecione **CA** e depois **State**.
1. Repita o processo de rotulagem para os formulários restantes na lista à esquerda. Rotule os mesmos quatro campos: *FirstName*, *LastName*, *City* e *State*.

> [!IMPORTANT]
> Neste exercício, estamos usando apenas cinco formulários de exemplo e rotulando apenas quatro campos. Nos modelos do mundo real, você deve usar o máximo de exemplos possível para maximizar a precisão e a confiança das previsões. Você também deve rotular todos os campos disponíveis nos formulários, em vez de apenas quatro campos.

## Treinar o modelo personalizado 1040 Forms

Agora que os formulários de exemplo estão rotulados, podemos treinar o primeiro modelo personalizado:

1. No Estúdio da IA do Azure para Informação de Documentos, selecione **Treinar**.
1. Na caixa de diálogo **Treinar um novo modelo**, na caixa de texto **ID do Modelo**, digite **1040FormsModel**.
1. Na lista suspensa **Modo de build**, selecione **Modelo** e depois **Treinar**. 
1. Na caixa de diálogo **Treinamento em andamento**, selecione **Acessar modelos**.

## Criar o modelo personalizado 1099 Forms

Agora, você precisa criar um segundo modelo, que você vai treinar para os exemplos de formulários de imposto 1099:

1. No Estúdio da IA do Azure para Informação de Documentos, selecione **Modelo personalizado**.
1. Em **Meus Projetos**, selecione **+ Criar um projeto**.
1. Na caixa de texto **Nome do projeto**, digite **1099 Forms** e selecione **Continuar**.
1. Na página **Configurar recurso de serviço**, na lista suspensa **Assinatura**, selecione sua assinatura do Azure.
1. Na lista suspensa **Grupo de recursos**, selecione **DocumentIntelligenceResources**.
1. Na lista suspensa **IA do Azure para Informação de Documentos ou Recurso do Serviço de IA do Azure**, selecione **DocumentIntelligence**
1. Na lista suspensa **Versão da API**, verifique se **2022-06-30-preview** está selecionado e clique em **Continuar**.

    :::image type="content" source="../media/4-configure-service-resources.png" alt-text="Captura de tela mostrando a página de recursos do serviço Configurar no assistente do modelo personalizado do Estúdio da IA do Azure para Informação de Documentos." lightbox="../media/4-configure-service-resources.png":::

1. Na página **Configurar fonte de dados de treinamento**, na lista suspensa **Assinatura**, selecione sua assinatura do Azure.
1. Na lista suspensa **Grupo de recursos**, selecione **DocumentIntelligenceResources**.
1. Na lista suspensa **Conta de armazenamento**, selecione a única conta de armazenamento listada.
1. Na lista suspensa **Contêiner de blob**, selecione **1099examples** e clique em **Continuar**.
1. Na página **Examinar e criar** , selecione **Criar projeto**.

## Rotular o modelo personalizado 1099 Forms

Agora, rotule os formulários de exemplo com alguns campos:

1. Na página **Dados de rótulo**, no canto superior direito da página, selecione **+** e depois **Campo**.
1. Digite **FirstName** e pressione <kbd>Enter</kbd>.
1. No documento, selecione **John** e depois **FirstName**.
1. No canto superior direito da página, selecione **+** e depois **Campo**.
1. Digite **LastName** e pressione <kbd>Enter</kbd>.
1. No documento, selecione **Doe** e depois **LastName**.
1. No canto superior direito da página, selecione **+** e depois **Campo**.
1. Digite **City** e pressione <kbd>Enter</kbd>.
1. No documento, selecione **New Haven** e depois **City**.
1. No canto superior direito da página, selecione **+** e depois **Campo**.
1. Digite **State** e pressione <kbd>Enter</kbd>.
1. No documento, selecione **CT** e depois **State**.
1. Repita o processo de rotulagem para os formulários restantes na lista à esquerda. Rotule os mesmos quatro campos: *FirstName*, *LastName*, *City* e *State*.

## Treinar o modelo personalizado 1099 Forms

Agora você pode treinar o segundo modelo personalizado:

1. No Estúdio da IA do Azure para Informação de Documentos, selecione **Treinar**.
1. Na caixa de diálogo **Treinar um novo modelo**, na caixa de texto **ID do Modelo**, digite **1099FormsModel**.
1. Na lista suspensa **Modo de build**, selecione **Modelo** e depois **Treinar**. 
1. Na caixa de diálogo **Treinamento em andamento**, selecione **Acessar modelos**.
1. O processo de treinamento pode levar alguns minutos. Atualize o navegador até que os dois modelos exibam o status **bem-sucedido**.

## Criar e montar um modelo composto

Os dois modelos personalizados, que analisam os formulários fiscais 1040 e 1099, agora estão concluídos. Você pode continuar para a criação do modelo composto:

1. Na página de Modelos da IA do Azure para Informação de Documentos, selecione **1040FormsModel** e **1099FormsModel**.
1. Na parte superior da lista de modelos, selecione **Compor**.

    :::image type="content" source="../media/4-start-compose-model.png" alt-text="Captura de tela mostrando como começar a compor um modelo no Estúdio da IA do Azure para Informação de Documentos." lightbox="../media/4-start-compose-model.png":::

1. Na caixa de diálogo **Compor um novo modelo**, na caixa de texto **ID do Modelo**, digite **TaxFormsModel** e **selecione Compor**. A IA do Azure para Informação de Documentos cria o modelo composto e o mostra na lista de modelos personalizados:

## Usar o modelo composto

Agora que o modelo composto está concluído, vamos testá-lo com um formulário de exemplo:

1. No [portal do Azure](https://portal.azure.com/learn.docs.microsoft.com?azure-portal=true), selecione **Todos os recursos** e escolha a conta de armazenamento **formsrecstorage&lt;xxxxx&gt;**, em que &lt;xxxxx&gt; é um número aleatório.
1. Em **Armazenamento de dados**, selecione **Contêineres** e depois **TestDoc**.
1. À direita do **f1040_7.pdf**, selecione **...** e depois **Baixar**.
1. Salve o documento PDF no computador local e anote o local salvo.
1. No Estúdio da IA do Azure para Informação de Documentos, selecione **TaxFormsModel** e, a seguir, selecione **Testar**.
1. Selecione **+ Adicionar** e navegue até o local em que você salvou o documento PDF.
1. Selecione **f1040_7.pdf** e depois **Abrir**.
1. Selecione **Analisar**. A IA do Azure para Informação de Documento analisa o formulário usando o modelo composto.

    :::image type="content" source="../media/4-composed-model-analysis.png" alt-text="Captura de tela mostrando como usar um modelo composto no Estúdio da IA do Azure para Informação de Documentos." lightbox="../media/4-composed-model-analysis.png":::

1. O documento analisado é um exemplo do formulário fiscal 1040. Verifique a propriedade **DocType** para saber se o modelo personalizado correto foi usado. Verifique também os valores **FirstName**, **LastName**, **City** e **State** identificados pelo modelo.

## Limpar os recursos do exercício

Agora que você viu como os modelos compostos funcionam, vamos remover os recursos que foram criados na assinatura do Azure.

1. No [portal do Azure](https://portal.azure.com/learn.docs.microsoft.com?azure-portal=true), selecione **Grupos de recursos**.
1. Na lista de **Grupos de recursos**, selecione **DocumentIntelligenceResources** e, a seguir, clique em **Excluir grupo de recursos**. 
1. Na caixa de texto **DIGITE O NOME DO GRUPO DE RECURSOS**, digite **DocumentIntelligenceResources** e, a seguir, selecione **Excluir**, para excluir o recurso da Informação de Documentos e a conta de armazenamento.

## Saiba mais

- [Compor modelos personalizados](/azure/ai-services/document-intelligence/concept-composed-models)
- [Criar seu conjunto de dados de treinamento para um modelo personalizado](/azure/applied-ai-services/form-recognizer/how-to-guides/build-custom-model-v3)
