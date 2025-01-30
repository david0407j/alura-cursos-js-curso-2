# Jogo do Número Secreto

Este é um jogo simples desenvolvido em JavaScript no qual o jogador tenta adivinhar um número secreto gerado aleatoriamente, com algumas funcionalidades extras, como a geração de números exclusivos e um contador de tentativas.

## Funcionalidades

1. **Geração de Números Aleatórios Exclusivos**
   - O número secreto é gerado de forma aleatória entre 1 e um número limite definido (`numeroLimite`). O número sorteado é garantido como exclusivo, ou seja, nenhum número será sorteado mais de uma vez.
   - Se todos os números possíveis já foram sorteados, a lista é reiniciada para que os números possam ser sorteados novamente.

2. **Feedback ao Jogador**
   - Após o jogador inserir seu chute, o jogo fornece feedback indicando se o número secreto é maior ou menor do que o número informado.
   - Quando o jogador acerta o número secreto, uma mensagem de vitória é exibida, informando o número de tentativas realizadas.

3. **Contador de Tentativas**
   - O jogo mantém um contador de tentativas, mostrando ao jogador quantas tentativas foram necessárias para adivinhar o número secreto.
   - A mensagem de vitória adapta automaticamente o termo "tentativa" ou "tentativas", conforme o número de tentativas feitas.

4. **Reinício do Jogo**
   - Após o jogador adivinhar o número secreto, o jogo oferece a opção de reiniciar, gerando um novo número secreto e permitindo novas tentativas.

## Código do Jogo

```javascript
let listaDeNumerosSorteados = [];
let numeroLimite = 10;
let numeroSecreto = gerarNumeroAleatorio();
let tentativas = 1;

function gerarNumeroAleatorio() {
  // Verifica se todos os números já foram sorteados
  if (listaDeNumerosSorteados.length === numeroLimite) {
    listaDeNumerosSorteados = []; // Reinicia a lista de números sorteados
  }

  let numeroEscolhido;
  do {
    numeroEscolhido = parseInt(Math.random() * numeroLimite + 1);
  } while (listaDeNumerosSorteados.includes(numeroEscolhido)); // Garante que o número não seja repetido

  listaDeNumerosSorteados.push(numeroEscolhido); // Adiciona o número à lista
  return numeroEscolhido;
}

// Exibe texto na tela e usa a biblioteca responsiveVoice
function exibirTextoNaTela(tag, texto) {
  let campo = document.querySelector(tag);
  campo.innerHTML = texto;
  if ('speechSynthesis' in window) {
      let utterance = new SpeechSynthesisUtterance(texto);
      utterance.lang = 'pt-BR'; 
      utterance.rate = 1.2; 
      window.speechSynthesis.speak(utterance); 
  } else {
      console.log("Web Speech API não suportada neste navegador.");
  }
}

// Inicializa as instruções do jogo
function exibirMensagemInicial() {
  exibirTextoNaTela('h1', 'Jogo do Número Secreto');
  exibirTextoNaTela('p', `Escolha um número entre 1 e ${numeroLimite}`);
}

// Verifica o chute do jogador
function verificarChute() {
  let chute = document.querySelector('input').value;

  // Valida o valor inserido
  if (!chute || isNaN(chute)) {
    exibirTextoNaTela('p', 'Por favor, insira um número válido!');
    limparCampo();
    return;
  }

  chute = parseInt(chute);

  if (chute === numeroSecreto) {
    exibirTextoNaTela('h1', 'Acertou!');
    let palavraTentativa = tentativas > 1 ? 'tentativas' : 'tentativa';
    let mensagemTentativas = `Você descobriu o número secreto com ${tentativas} ${palavraTentativa}!`;
    exibirTextoNaTela('p', mensagemTentativas);
    document.getElementById('reiniciar').removeAttribute('disabled'); // Habilita o botão de reiniciar
  } else {
    if (chute > numeroSecreto) {
      exibirTextoNaTela('p', 'O número secreto é menor');
    } else {
      exibirTextoNaTela('p', 'O número secreto é maior');
    }
    tentativas++;
    limparCampo();
  }
}

function limparCampo() {
  let campo = document.querySelector('input');
  campo.value = ''; // Limpa o valor do campo
}

// Reinicia o jogo
function reiniciarJogo() {
  numeroSecreto = gerarNumeroAleatorio(); // Gera um novo número secreto
  limparCampo();
  tentativas = 1; // Reseta o contador de tentativas
  exibirMensagemInicial(); // Exibe a mensagem inicial novamente
  document.getElementById('reiniciar').setAttribute('disabled', true); // Desabilita o botão de reiniciar
}

// Inicializa o jogo
exibirMensagemInicial();
document.getElementById('reiniciar').setAttribute('disabled', true); // Desabilita o botão de reiniciar inicialmente
