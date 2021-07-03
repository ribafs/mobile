# Breakout com o Monaca usando a CLI

## Instalação do Monaca CLI

sudo npm install -g monaca

## Jogo original web

Código fonte - https://github.com/end3r/Gamedev-Phaser-Content-Kit

Tutorial em inglês - http://end3r.github.io/Gamedev-Phaser-Content-Kit/

Jogo completo em inglês - https://end3r.github.io/Gamedev-Phaser-Content-Kit/demos/lesson16.html

O jogo está na pasta

breakout

O projeto criado pelo Monaca na pasta

breakout-monaca

## Logar com o servidor na nuvem

Entre com e-mail e senha

monaca login

## Criando o projeto padrão do Monaca sem framework e com o template Blank

monaca create breakout-monaca

Selecion No Framework

Selecionar o template Blank

Aguarde o download do tempalte blank

Após concluir mostra

  > monaca preview      => Run app in the browser
  > monaca debug        => Run app in the device using Monaca Debugger
  > monaca remote build => Start remote build for iOS/Android/Windows
  > monaca upload       => Upload this project to Monaca Cloud IDE

cd breakout-monaca

run preview - abrirá automaticamente no navegador

## Adaptar o projeto criado para o jogo breakout

## Regra básica

Manter todo o código do original criado pelo Monaca e adicionar o código do breakout

## Código gerado pelo Monaca

## Estrutura criada pelo Monaca em breakout-monaca

node_modules
res
www
config.xml
LICENSE
package.json
package-lock.json

www
	components/
	css/
		style.css
	index.html
	
Somente alteraremos o conteúdo da pasta www	
	
## Estrutura do jogo na pasta breakout

img/
js/
	phaser.2.4.2.min.js
index.html

- Iniciarei copiando as pastas img e js para o projeto criado pelo Monaca.

- Agora fazer um merge do www/index.html com o conteúdo do index.html do jogo

www/index.html do breakout-monaca

<!DOCTYPE HTML>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    <meta http-equiv="Content-Security-Policy" content="default-src * data: gap: content: https://ssl.gstatic.com; style-src * 'unsafe-inline'; script-src * 'unsafe-inline' 'unsafe-eval'">
    <script src="components/loader.js"></script>
    <link rel="stylesheet" href="components/loader.css">
    <link rel="stylesheet" href="css/style.css">
    <script>
    </script>
</head>
<body>
	<br />
    This is a template for Monaca app.
</body>
</html>

index.html do breakout

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Breakout game in Phaser</title>
    <style>* { padding: 0; margin: 0; }</style>
    <script src="js/phaser.2.4.2.min.js"></script>
</head>
<body>
<script>
...
Todo o script js abaixo.

Juntando o código do index.html do breakout com o do www/index.html

O código completo do www/index.html já com o do jogo ficou assim:

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Breakout game in Phaser</title>
    <style>* { padding: 0; margin: 0; }</style>
    <script src="js/phaser.2.4.2.min.js"></script>
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    <meta http-equiv="Content-Security-Policy" content="default-src * data: gap: content: https://ssl.gstatic.com; style-src * 'unsafe-inline'; script-src * 'unsafe-inline' 'unsafe-eval'">
    <script src="components/loader.js"></script>
    <link rel="stylesheet" href="components/loader.css">
    <link rel="stylesheet" href="css/style.css">
    <script>
    </script>    
</head>
<body>
<script>
var game = new Phaser.Game(480, 320, Phaser.AUTO, null, {preload: preload, create: create, update: update});

var ball;
var paddle;
var bricks;
var newBrick;
var brickInfo;
var scoreText;
var score = 0;
var lives = 3;
var livesText;
var lifeLostText;
var playing = false;
var startButton;

function preload() {
    game.scale.scaleMode = Phaser.ScaleManager.SHOW_ALL;
    game.scale.pageAlignHorizontally = true;
    game.scale.pageAlignVertically = true;
    game.stage.backgroundColor = '#eee';
    game.load.image('paddle', 'img/paddle.png');
    game.load.image('brick', 'img/brick.png');
    game.load.spritesheet('ball', 'img/wobble.png', 20, 20);
    game.load.spritesheet('button', 'img/button.png', 120, 40);
}
function create() {
    game.physics.startSystem(Phaser.Physics.ARCADE);
    game.physics.arcade.checkCollision.down = false;
    ball = game.add.sprite(game.world.width*0.5, game.world.height-25, 'ball');
    ball.animations.add('wobble', [0,1,0,2,0,1,0,2,0], 24);
    ball.anchor.set(0.5);
    game.physics.enable(ball, Phaser.Physics.ARCADE);
    ball.body.collideWorldBounds = true;
    ball.body.bounce.set(1);
    ball.checkWorldBounds = true;
    ball.events.onOutOfBounds.add(ballLeaveScreen, this);

    paddle = game.add.sprite(game.world.width*0.5, game.world.height-5, 'paddle');
    paddle.anchor.set(0.5,1);
    game.physics.enable(paddle, Phaser.Physics.ARCADE);
    paddle.body.immovable = true;

    initBricks();

    textStyle = { font: '18px Arial', fill: '#0095DD' };
    scoreText = game.add.text(5, 5, 'Pontos: 0', textStyle);
    livesText = game.add.text(game.world.width-5, 5, 'Vidas: '+lives, textStyle);
    livesText.anchor.set(1,0);
    lifeLostText = game.add.text(game.world.width*0.5, game.world.height*0.5, 'Você perdeu uma vida. Clique para continuar', textStyle);
    lifeLostText.anchor.set(0.5);
    lifeLostText.visible = false;

    adaptacaoText = game.add.text(game.world.width*0.5, game.world.height*0.8, 'Tradução de Ribamar FS - http://ribafs.org', textStyle);
    adaptacaoText.anchor.set(0.5);
    adaptacaoText.visible = true;    
    
    startButton = game.add.button(game.world.width*0.5, game.world.height*0.5, 'button', startGame, this, 1, 0, 2);
    startButton.anchor.set(0.5);
}
function update() {
    game.physics.arcade.collide(ball, paddle, ballHitPaddle);
    game.physics.arcade.collide(ball, bricks, ballHitBrick);
    if(playing) {
        paddle.x = game.input.x || game.world.width*0.5;
    }
}
function initBricks() {
    brickInfo = {
        width: 50,
        height: 20,
        count: {
            row: 7,
            col: 3
        },
        offset: {
            top: 50,
            left: 60
        },
        padding: 10
    }
    bricks = game.add.group();
    // Renderizando os tijolos na tela
    for(c=0; c<brickInfo.count.col; c++) {
        for(r=0; r<brickInfo.count.row; r++) {
            var brickX = (r*(brickInfo.width+brickInfo.padding))+brickInfo.offset.left;
            var brickY = (c*(brickInfo.height+brickInfo.padding))+brickInfo.offset.top;
            newBrick = game.add.sprite(brickX, brickY, 'brick');
            game.physics.enable(newBrick, Phaser.Physics.ARCADE);
            newBrick.body.immovable = true;
            newBrick.anchor.set(0.5);
            bricks.add(newBrick);
        }
    }
}
function ballHitBrick(ball, brick) {
    var killTween = game.add.tween(brick.scale);
    killTween.to({x:0,y:0}, 200, Phaser.Easing.Linear.None);
    killTween.onComplete.addOnce(function(){
        brick.kill();
    }, this);
    killTween.start();
    score += 10;
    scoreText.setText('Pontos: '+score);
    if(score === brickInfo.count.row*brickInfo.count.col*10) {
        alert('Você venceu o jogo, parabéns!');
        location.reload();
    }
}
function ballLeaveScreen() {
    lives--;
    if(lives) {
        livesText.setText('Vidas: '+lives);
        lifeLostText.visible = true;
        ball.reset(game.world.width*0.5, game.world.height-25);
        paddle.reset(game.world.width*0.5, game.world.height-5);
        game.input.onDown.addOnce(function(){
            lifeLostText.visible = false;
            ball.body.velocity.set(150, -150);
        }, this);
    }
    else {
        alert('Você perdeu, game over!');
        location.reload();
    }
}
function ballHitPaddle(ball, paddle) {
    ball.animations.play('wobble');
    ball.body.velocity.x = -1*5*(paddle.x-ball.x);
}
function startGame() {
    startButton.destroy();
    ball.body.velocity.set(150, -150);
    playing = true;
}
</script>
</body>
</html>

## Testar no navegador

cd breakout-monaca

monaca preview

## Gerando o build

Opções de build

  $ monaca remote build ios
  $ monaca remote build ios --build-type=debug
  $ monaca remote build android --build-type=debug --android_webview=crosswalk --android_arch=arm
  $ monaca remote build pwa --build-type=release
  $ monaca remote build electron_windows
  $ monaca remote build electron_macos
  $ monaca remote build electron_linux
  $ monaca remote build --browser
  $ monaca remote build --build-list
  $ monaca remote build android --skipUpload
  $ monaca remote build --browser --skipTranspile

monaca remote build android

Alertará que esta operação irá sobrescrever projetos com o mesmo nome no servidor

monaca remote build android
This operation will overwrite all the remote changes that have been made.
? Do you want to continue? (y/N) 

Tecle y e enter

Creating a new project in cloud...
? Project Name: (brekout) 

Apenas tecle enter para aceitar o nome breakout ou modifique e enter

Entre com uma descrição para o Jogo e tecle enter

Creating a new project in cloud...
? Project Name: brekout
? Project Description: (monaca-project) Pequeno jogo tipo breakout

Creating a new project in cloud...
? Project Name: brekout
? Project Description: Pequeno jogo tipo breakout
Uploading project to Monaca Cloud...
✔ Comparing Files: DONE
[9.090%] /config.xml
[18.18%] /www/img/ball.png
[27.27%] /package.json
[36.36%] /package-lock.json
[45.45%] /www/img/button.png
[54.54%] /www/img/brick.png
[63.63%] /www/img/mdn-breakout-phaser.png
[72.72%] /www/img/paddle.png
[81.81%] /www/img/wobble.png
[90.90%] /www/index.html
[100%] /www/js/phaser.2.4.2.min.js

Project is successfully uploaded to Monaca Cloud!

O build será feito no site, pois nas duas vezes que tentei fazer com a CLI não funcionou.

Acessar o site

https://console.monaca.mobi/dashboard

Selecionar o projeto breakout.

Abrir na IDE

Clicar acima em Build

Antes do Build vamos efetuar configurações. Clique em App Configuration em Android

Em
Package Name - me.ribafs.breakout

Adapte para você (meu domínio: ribafs.me)

Role a tela e clique em Save

Voltar para a IDE e clicar em Remote Build em Android

Clique em Build for Release

Clique em Start e aguarde

Quando concluir oferece 3 alternativas para a instalação:
- Download do apk
- QRCode
- Envio do link para seu e-mail

Salve pois ele não guarda.

- Eu envio o link por e-mail
- Instalo o app do Firefox no celular
- Abro o gmail no app do Firefox
- Abro o link e faço o download do apk
- Instalo o app Apk Installer
- Abro o Apk Installer e ele já detecta o apk baixado
- Então instalo
E está lá o breakout

É interessante criar um ícone personalizado para o aplicativo a ser informado nas configurações e também ícones para o Android.

