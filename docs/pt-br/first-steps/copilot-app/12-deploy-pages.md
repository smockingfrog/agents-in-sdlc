---
title: "Lição 12 - Implantar o site com o GitHub Pages"
description: "Use o aplicativo GitHub Copilot para configurar um fluxo de trabalho do Pages, revisar o pull request e verificar o Space Quiz publicado."
authors:
  - jamesmontemagno
lastUpdated: 2026-10-12
---

Dê ao Space Quiz um endereço público implantando-o com o GitHub Pages. O Copilot pode preparar o fluxo de trabalho e orientar a implantação, mas você continua responsável por revisar as alterações e confirmar que o site está disponível.

Nesta lição, você vai:

- pedir ao Copilot que configure a implantação no GitHub Pages.
- revisar o fluxo de trabalho e o pull request antes do merge.
- acompanhar a implantação e verificar o site publicado.

## Cenário

O Space Quiz já está em um repositório do GitHub, mas as pessoas ainda precisam de uma maneira simples de experimentá-lo. Publicar o site de arquivo único com o GitHub Pages disponibiliza o aplicativo em um URL público, sem adicionar um servidor ou dependência de runtime.

## Pedir ao Copilot que prepare a implantação

> [!WARNING]
> A criação de um pull request pode fazer commit e enviar código. Revise as configurações de aprovação e o repositório de destino antes de enviar este prompt. Com **Approve all**, não espere uma solicitação de confirmação separada.

Na sessão do aplicativo Copilot para `space-quiz`, envie o prompt:

```plaintext
Prepare a GitHub Pages deployment for this repository. Create a GitHub Actions workflow that builds the website if needed and deploys it to GitHub Pages after changes reach the default branch. Add a README describing the app and how to run it locally if one does not already exist. Create a pull request with these changes and report its link and any required Pages settings or permissions. Do not merge, enable auto-merge, change Pages settings, or deploy. Stop and wait for my review.
```

## Revisar o fluxo de trabalho e o pull request

1. Confirme se o README foi adicionado somente se o repositório ainda não tinha um.
2. Revise as alterações do pull request, incluindo as etapas de compilação e implantação, os gatilhos e as permissões do fluxo de trabalho.
3. Confirme se o fluxo implanta a partir da branch padrão, e não de um pull request ainda não integrado.
4. Solicite correções, se necessário, e revise o diff atualizado. Aguarde a aprovação das verificações obrigatórias do pull request antes de autorizar o merge.

## Autorizar o merge e a implantação

> [!WARNING]
> O prompt a seguir autoriza o agente a fazer merge do pull request revisado, alterar as configurações do Pages do repositório e publicar o site. Envie-o somente depois de concluir a revisão e confirmar o repositório e o pull request. Dependendo das configurações de aprovação, essas ações podem ocorrer sem outra solicitação de confirmação.

Substitua `<PR_NUMBER>` pelo número do pull request revisado e envie este prompt separado:

```plaintext
I have reviewed pull request <PR_NUMBER> in this repository and authorize its merge and deployment. Enable GitHub Pages using GitHub Actions, merge that pull request only after required checks pass, and monitor the deployment until it succeeds. Report the final URL of the live website. If you lack permission to enable Pages or merge, tell me exactly what I need to approve or do; do not report success until the site is live.
```

![Diagrama em três etapas: um pull request do space-quiz integrado; uma execução bem-sucedida de Deploy website no GitHub Actions com as etapas Build website, Upload artifact e Deploy to Pages; e o Space Quiz publicado em yourname.github.io/space-quiz/.](../../../_images/first-steps-app-pages-deploy.svg)

## Verificar a implantação

1. Acompanhe a execução de implantação do GitHub Actions até que ela seja concluída com sucesso.
2. Abra o URL informado e confirme se o Space Quiz carrega.

Se o agente não puder habilitar o Pages ou fazer merge do pull request, siga as instruções específicas que ele fornecer. Não considere a tarefa concluída até que a implantação seja bem-sucedida e o site publicado esteja carregando.

## Resumo e próximos passos

O Space Quiz está disponível como um site publicado, e você verificou a implantação em vez de confiar apenas no relatório do agente. Continue para [Lição 13: Revisão e próximos passos][next-lesson].

[next-lesson]: ../13-review/
