# Pagamento.PHP
Pagina de pagamento da lojinha.

<?php
// Inclui o script de segurança que verifica se o usuário está autenticado //
include('segurancaum.php');

// Inclui o cabeçalho da página (HTML padrão, menus, etc.) //
include('cabecalho.php');

// Inclui o arquivo de conexão com o banco de dados //
include('conn.php');

// Verifica se existe uma sessão de carrinho ativa; caso não exista, redireciona para a página inicial //
session_start(); // Inicia a sessão para acessar as variáveis de sessão //
if(!isset($_SESSION['carrinho'])) {
    header('Location: index.php'); // Redireciona para a página inicial //
    exit(); // Encerra o script para evitar execução adicional //
}
//Pegar os dados do usuário//
// Busca os dados do usuário logado a partir do ID armazenado na sessão //
$sql = "SELECT * FROM tb_usuarios WHERE id_usuario = {$_SESSION['id_usuario']}";
$result = mysqli_query($link, $sql);
$usuario = mysqli_fetch_array($result); // Armazena os dados do usuário em um array //
?>
<!DOCTYPE html>
<html lang="PT-br">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- Link para o arquivo de estilos CSS -->
    <link rel="stylesheet" href="estilo3.css">
    <title>Pagamento</title>
</head>

<body>
    <br>
    <h1>Pagamento</h1>
    <br><br>

<table>
        <tr>
            <td colspan="5">
                <!-- Exibe os dados do usuário para conferência -->
                <b>Nome: </b><?= $usuario[1] ?><br>
                <b>Telefone: </b><?= $usuario[14] ?> - <b>CPF: </b><?= $usuario[3] ?><br>
                <b>Email: </b><?= $usuario[4] ?><br>
                <b>Dados de Entrega:</b><br>
                <?= $usuario[6] ?>, <?= $usuario[7] ?> - <?= $usuario[8] ?><br>
                <?= $usuario[9] ?> - <?= $usuario[10] ?><br>
                <b>CEP: </b><?= $usuario[5] ?><br>
            </td>
        </tr>

<?php
        // Busca os produtos adicionados ao carrinho com seus detalhes e calcula o valor total //
        $sql = "SELECT id_carrinho, id_produto_carrinho, nome_produto, valor_produto,
            (valor_produto * quantidade_carrinho), imagem_produto, quantidade_carrinho
            FROM tb_carrinhos
            JOIN tb_produtos ON tb_carrinhos.id_produto_carrinho = tb_produtos.id_produto
            WHERE numero_carrinho = '{$_SESSION['carrinho']}'";
        $result = mysqli_query($link, $sql);

        // Fecha a conexão com o banco de dados (após a consulta) //
        mysqli_close($link);

        $total = 0; // Variável para somar o valor total da compra //

        // Loop que exibe cada produto do carrinho //
        while ($tbl = mysqli_fetch_array($result)) {
        ?>
<tr>
                <td><img src="imagens/<?= $tbl[5] ?>" alt="Imagem Produto" height="50"></td>
                <td><?= $tbl[2] ?></td> <!-- Nome do produto -->
                <td>R$ <?= number_format($tbl[3], 2, ",", ".") ?> </td> <!-- Preço unitário -->
                <td><?= $tbl[6] ?></td> <!-- Quantidade -->
                <td>R$ <?= number_format($tbl[4], 2, ",", ".") ?> </td> <!-- Subtotal -->
            </tr>
        <?php
            $total += $tbl[4]; // Soma o subtotal ao total geral //
        }
        ?>

        <!-- Exibe o total geral da compra -->
<tr>
            <th></th>
            <th></th>
            <th></th>
            <th>Total Geral</th>
            <th>R$ <?= number_format($total, 2, ",", ".") ?> </th>
            <th></th>
        </tr>
    </table>
<br>

    <!-- Formulário para o usuário selecionar o método de pagamento -->
<form action="obrigado.php" method="post">
        <fieldset>
            <legend>Método de Pagamento</legend>
            <label for="pix">PIX</label>
            <input type="radio" name="pagamento" id="pix" value="2" checked>
            <label for="cartao">Cartão de Crédito</label>
            <input type="radio" name="pagamento" id="cartao" value="3">
            <label for="boleto">Boleto Bancário</label>
            <input type="radio" name="pagamento" id="boleto" value="4">
        </fieldset>

<br><br>

        <!-- Botão para voltar ao carrinho -->
<a href="carrinho.php"><button type="button">Voltar ao Carrinho</button></a>

        <!-- Botão para confirmar o pagamento -->
<input type="submit" value="Confirmar Pagamento">
    </form>
</body>

</html>
