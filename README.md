<!DOCTYPE html><html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Loja de Vitamínicos</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@2.6.4/dist/email.min.js"></script>
  <script>
    (function(){
      emailjs.init("SUA_PUBLIC_KEY"); // Substitua aqui com sua public key do EmailJS
    })();
  </script>
  <style>
    body { background-color: #f8f9fa; }
    .produto img { height: 150px; object-fit: cover; }
    .remover { margin-left: 10px; }
    #checkoutForm, #recibo { display: none; }
  </style>
</head>
<body>
  <header class="bg-success text-white text-center p-4 position-relative">
    <h1>Loja Natural Saúde</h1>
    <p>Vitamínicos e Remédios para seu bem-estar</p>
    <div class="position-absolute top-0 end-0 m-3 bg-dark px-3 py-2 rounded">
      Carrinho: <span id="contador">0</span>
    </div>
  </header>  <div class="container my-4">
    <div class="row" id="produtos">
      <div class="col-md-4">
        <div class="card">
          <img src="https://via.placeholder.com/250x150" class="card-img-top" alt="Vitamina C">
          <div class="card-body">
            <h5 class="card-title">Vitamina C 1000mg</h5>
            <p class="card-text">Fortalece o sistema imunológico e combate o cansaço.</p>
            <p class="text-success fw-bold">R$ 29,90</p>
            <button class="btn btn-success" onclick="adicionarAoCarrinho('Vitamina C 1000mg', 29.9)">Comprar</button>
          </div>
        </div>
      </div>
      <div class="col-md-4">
        <div class="card">
          <img src="https://via.placeholder.com/250x150" class="card-img-top" alt="Ômega 3">
          <div class="card-body">
            <h5 class="card-title">Ômega 3</h5>
            <p class="card-text">Ajuda na saúde cardiovascular e função cerebral.</p>
            <p class="text-success fw-bold">R$ 39,90</p>
            <button class="btn btn-success" onclick="adicionarAoCarrinho('Ômega 3', 39.9)">Comprar</button>
          </div>
        </div>
      </div>
      <div class="col-md-4">
        <div class="card">
          <img src="https://via.placeholder.com/250x150" class="card-img-top" alt="Pomada">
          <div class="card-body">
            <h5 class="card-title">Pomada Anti-inflamatória</h5>
            <p class="card-text">Alivia dores musculares e inflamações.</p>
            <p class="text-success fw-bold">R$ 19,90</p>
            <button class="btn btn-success" onclick="adicionarAoCarrinho('Pomada Anti-inflamatória', 19.9)">Comprar</button>
          </div>
        </div>
      </div>
    </div><div class="card mt-5">
  <div class="card-body">
    <h3>Itens no Carrinho</h3>
    <ul class="list-group" id="itens"></ul>
    <p class="mt-3 fw-bold">Total: R$ <span id="total">0.00</span></p>
    <button class="btn btn-warning" onclick="mostrarCheckout()">Finalizar Compra</button>
  </div>
</div>

<form id="checkoutForm" class="card mt-4 p-4">
  <h4>Checkout</h4>
  <div class="mb-3">
    <label class="form-label">Nome</label>
    <input type="text" class="form-control" id="nome" required>
  </div>
  <div class="mb-3">
    <label class="form-label">E-mail</label>
    <input type="email" class="form-control" id="email" required>
  </div>
  <div class="mb-3">
    <label class="form-label">Endereço</label>
    <textarea class="form-control" id="endereco" rows="2" required></textarea>
  </div>
  <button type="submit" class="btn btn-primary">Enviar Pedido</button>
</form>

<div id="recibo" class="card mt-4 p-4">
  <h4>Recibo da Compra</h4>
  <div id="dadosRecibo"></div>
  <a id="whatsappLink" class="btn btn-success mt-3" target="_blank">Enviar via WhatsApp</a>
</div>

  </div>  <script>
    let carrinho = JSON.parse(localStorage.getItem('carrinho')) || [];

    function salvarCarrinho() {
      localStorage.setItem('carrinho', JSON.stringify(carrinho));
    }

    function atualizarContador() {
      document.getElementById('contador').textContent = carrinho.length;
    }

    function adicionarAoCarrinho(nome, preco) {
      carrinho.push({ nome, preco });
      salvarCarrinho();
      atualizarListaCarrinho();
    }

    function removerDoCarrinho(index) {
      carrinho.splice(index, 1);
      salvarCarrinho();
      atualizarListaCarrinho();
    }

    function atualizarListaCarrinho() {
      const lista = document.getElementById('itens');
      const total = document.getElementById('total');
      lista.innerHTML = '';
      let soma = 0;
      carrinho.forEach((item, index) => {
        const li = document.createElement('li');
        li.className = 'list-group-item d-flex justify-content-between align-items-center';
        li.innerHTML = `${item.nome} - R$ ${item.preco.toFixed(2)} <button class="btn btn-danger btn-sm remover" onclick="removerDoCarrinho(${index})">Remover</button>`;
        lista.appendChild(li);
        soma += item.preco;
      });
      total.textContent = soma.toFixed(2);
      atualizarContador();
    }

    function mostrarCheckout() {
      if (carrinho.length === 0) {
        alert('Seu carrinho está vazio.');
        return;
      }
      document.getElementById('checkoutForm').style.display = 'block';
    }

    document.getElementById('checkoutForm').addEventListener('submit', function (e) {
      e.preventDefault();
      const nome = document.getElementById('nome').value;
      const email = document.getElementById('email').value;
      const endereco = document.getElementById('endereco').value;
      const total = document.getElementById('total').textContent;
      const data = new Date().toLocaleString();

      const itensTexto = carrinho.map(i => `- ${i.nome} (R$ ${i.preco.toFixed(2)})`).join('\n');

      const recibo = `Nome: ${nome}\nE-mail: ${email}\nEndereço: ${endereco}\nData: ${data}\nItens:\n${itensTexto}\nTotal: R$ ${total}`;
      localStorage.setItem('ultimoPedido', recibo);

      // Mostrar recibo
      document.getElementById('dadosRecibo').innerText = recibo;
      document.getElementById('recibo').style.display = 'block';

      // WhatsApp
      const msg = encodeURIComponent(recibo);
      document.getElementById('whatsappLink').href = `https://wa.me/?text=${msg}`;

      // EmailJS
      emailjs.send("SEU_SERVICE_ID", "SEU_TEMPLATE_ID", {
        nome_cliente: nome,
        email_cliente: email,
        endereco_cliente: endereco,
        lista_itens: itensTexto,
        total: total,
        data: data
      })
      .then(() => alert("Pedido enviado por e-mail com sucesso!"))
      .catch((error) => console.error("Erro ao enviar e-mail:", error));

      // Limpar carrinho
      carrinho = [];
      salvarCarrinho();
      atualizarListaCarrinho();
      this.reset();
      this.style.display = 'none';
    });

    atualizarListaCarrinho();
  </script></body>
</html># Vitaminas-Life-s
