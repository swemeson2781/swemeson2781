const { Client, GatewayIntentBits } = require('discord.js');
const client = new Client({ intents: [GatewayIntentBits.Guilds, GatewayIntentBits.GuildMessages, GatewayIntentBits.MessageContent] });

const chavePix = 'sua-chave-pix-aqui';
const adminID = 'seu-id-admin';

client.once('ready', () => {
  console.log('Bot está online!');
});

client.on('messageCreate', (message) => {
  if (message.content.startsWith('!comprar')) {
    message.reply(`Para realizar a compra, envie o valor para nossa chave PIX: **${chavePix}** e depois digite o comando **!confirmar** com o comprovante.`);
  }

  if (message.content.startsWith('!confirmar')) {
    const args = message.content.split(' ').slice(1); // Pega o comprovante
    const comprovante = args.join(' ');

    if (!comprovante) {
      message.reply("Por favor, envie seu comprovante de pagamento.");
    } else {
      message.reply("Pagamento recebido! Aguardando verificação...");

      // Notifica o admin
      client.users.fetch(adminID).then((admin) => {
        admin.send(`Novo pagamento confirmado! Comprovante: ${comprovante}.`);
      });
    }
  }

  // Comando de envio do produto (somente para o administrador)
  if (message.content.startsWith('!enviar')) {
    if (message.author.id === adminID) {
      const args = message.content.split(' ').slice(1);
      const compradorID = args[0];
      const produto = args.slice(1).join(' ');

      client.users.fetch(compradorID).then((comprador) => {
        comprador.send(`Aqui está seu produto: ${produto}`);
        message.reply(`Produto enviado para o usuário ${compradorID}.`);
      });
    } else {
      message.reply("Você não tem permissão para usar este comando.");
    }
  }
});

client.login('SEU_TOKEN_DISCORD');
