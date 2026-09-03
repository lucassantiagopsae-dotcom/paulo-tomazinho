# UTMs e Eventos - GHL

## Parametros que devem ser preservados

- `utm_source`
- `utm_medium`
- `utm_campaign`
- `utm_content`
- `utm_term`
- `gclid`
- `fbclid`
- `msclkid`

## Regra de comportamento

Quando a pessoa entra na landing com parametros de campanha, todos os links para o quiz/formulario devem receber os mesmos parametros. Exemplo:

Entrada:
`/diagnostico-didatico?utm_source=meta&utm_medium=paid&utm_campaign=instituicoes`

Saida para o quiz:
`/quiz-diagnostico?utm_source=meta&utm_medium=paid&utm_campaign=instituicoes`

## Eventos recomendados

- `diagnostico_lp_view`: visualizacao da landing.
- `diagnostico_quiz_click`: clique no CTA que leva ao quiz.
- `diagnostico_quiz_start`: inicio do quiz/formulario.
- `diagnostico_quiz_submit`: envio do quiz/formulario.
- `diagnostico_thank_you_view`: visualizacao da pagina de confirmacao.

## Script base para preservar UTMs em links

Inserir no rodape da landing no GHL, ajustando o seletor dos botoes se necessario.

```html
<script>
  (function () {
    var keys = ["utm_source", "utm_medium", "utm_campaign", "utm_content", "utm_term", "gclid", "fbclid", "msclkid"];
    var current = new URLSearchParams(window.location.search);
    var carried = new URLSearchParams();

    keys.forEach(function (key) {
      var value = current.get(key);
      if (value) carried.set(key, value);
    });

    if (!carried.toString()) return;

    document.querySelectorAll("[data-quiz-link]").forEach(function (link) {
      try {
        var target = new URL(link.href, window.location.href);
        carried.forEach(function (value, key) {
          target.searchParams.set(key, value);
        });
        link.href = target.toString();
      } catch (error) {
        // Link placeholder or invalid URL. Keep original href.
      }
    });
  })();
</script>
```

## Teste rapido

1. Abrir a landing com `?utm_source=qa&utm_medium=test&utm_campaign=diagnostico`.
2. Clicar no botao do quiz.
3. Confirmar se a URL do quiz manteve os parametros.
4. Enviar um lead de teste.
5. Confirmar o registro do contato no GHL e os eventos nas ferramentas de analytics.
