# HunHive
const { Client, GatewayIntentBits } = require("discord.js");
const { joinVoiceChannel, createAudioPlayer, createAudioResource } = require("@discordjs/voice");
const ytdl = require("ytdl-core");

const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildVoiceStates,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.MessageContent
  ]
});

client.once("ready", () => {
  console.log(`✅ Logged in as ${client.user.tag}`);
});

client.on("messageCreate", async (message) => {
  if (!message.content.startsWith("!play") || message.author.bot) return;

  const args = message.content.split(" ");
  const url = args[1];

  if (!url) return message.reply("❌ Please give me a YouTube link!");

  const voiceChannel = message.member.voice.channel;
  if (!voiceChannel) return message.reply("❌ You need to be in a voice channel first!");

  const connection = joinVoiceChannel({
    channelId: voiceChannel.id,
    guildId: message.guild.id,
    adapterCreator: message.guild.voiceAdapterCreator,
  });

  const stream = ytdl(url, { filter: "audioonly" });
  const resource = createAudioResource(stream);
  const player = createAudioPlayer();

  player.play(resource);
  connection.subscribe(player);

  message.reply(`🎶 Now playing: ${url}`);
});

client.login("YOUR_BOT_TOKEN");
