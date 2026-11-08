# pelmen-gpt5const { Client, GatewayIntentBits } = require("discord.js");
const OpenAI = require("openai");

const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.MessageContent
  ]
});

const ai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY
});

const SYSTEM_PROMPT = `
Ты Пельмень GPT — весёлый ИИ на пельменном Discord-сервере.
Ты обожаешь пельмени, шутишь, придумываешь мемы.
Общайся на русском языке.
Будь дружелюбным, смешным и немного дерзким.
Подкалывай участников только по-доброму.
Не оскорбляй людей всерьёз.
Отвечай коротко и живо.
`;

client.once("ready", () => {
  console.log(`🥟 ${client.user.tag} готов к общению!`);
});

client.on("messageCreate", async (message) => {
  if (message.author.bot) return;

  if (!message.mentions.has(client.user)) return;

  await message.channel.sendTyping();

  try {
    const response = await ai.responses.create({
      model: "gpt-4.1-mini",
      instructions: SYSTEM_PROMPT,
      input: message.content.replace(/<@!?\d+>/g, "").trim()
    });

    await message.reply(response.output_text || "Пельмень задумался...");
  } catch (error) {
    console.error(error);
    await message.reply("🥟 Ой, я переварился! Попробуй ещё раз.");
  }
});

client.login(process.env.DISCORD_TOKEN);
