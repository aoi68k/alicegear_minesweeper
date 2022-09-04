# alicegear_minesweeper
<html>
<head>
</head>
<body bgcolor="#fff">
<div id=Retry style="position:absolute;background-image:url('hexBuff.png');background-size: 64px;height:64px;width:64px;text-align:center;"onClick="Retry();">RETRY</div>
<div id=info style="position:absolute;left:90px;top:24px;"></div>
<div id=map></div>
<div id=GameClear style="display:none;
position:absolute;
vertical-align:middle;
text-align:center;
font-size:48pt;
font-weight:900;
font-style:italic;
color:#FFF;
-webkit-text-stroke: 3px #000;
font-family: Times New Roman, 'ＭＳ Ｐゴシック';"></div>
</body>

<script>
var map_x=15,map_y=10;//マップの広さ
var mine_max=15;//マインの数
var open_num;//開けた数
var isGameOver;
var map=[], mine=[], prog=[];
function init(){//初期化
	console.log("init");
	open_num=0;
	isGameOver=0;
	for(var x=0;x<map_x;x++){
		map[x]=Array();
		mine[x]=Array();
		prog[x]=Array();
	}
	for(var y=0;y<map_y;y++){
		for(var x=0;x<map_x;x++){
			map[x][y]=0;
			mine[x][y]=0;
			prog[x][y]=0;
		}
	}
	document.getElementById("GameClear").style.display="none";
	document.getElementById("GameClear").innerHTML="";
	document.getElementById('GameClear').style.width=map_x*52;
	document.getElementById('GameClear').style.height=map_y*50;
	//document.getElementById('Retry').style.top=map_y*60;
	updateInfo();
}
function disp(){//盤面表示
	console.log("disp");
	document.getElementById('map').innerHTML="";
	for(var y=0;y<map_y;y++){
		for(var x=0;x<map_x;x++){
			xx=x*56;
			yy=y*56+(x%2==1)*28+64;
			click="oncontextmenu='return hexLock("+x+","+y+");'onClick='hexOpen("+x+","+y+")'";
			color='#777';
			if(mine[x][y]==1)color='#f77';
			number='';
			//if(map[x][y]!=0)number=map[x][y];
			document.getElementById('map').innerHTML+="<img\
			 src=hex.png height=64 width=64\
			 id="+x+"_"+y+"\
			 "+click+"\
			 style='position:absolute;top:"+yy+"px;left:"+xx+"px;'>"
			 +"<box\
			 id=t"+x+"_"+y+"\
			 style='position:absolute;top:"+(yy+14)+"px;left:"+(xx+25)+"px;\
			 font-family:impact;font-size:28px;color:#777777;'\
			 "+click+">"+"<box style='color:"+color+"'>"+number+"</box>"+"</box>"
			 +"</img>";
		}
		document.getElementById('map').innerHTML+="<br>";
	}
}
function deploy(x,y){//マイン生成
	console.log("deploy:"+x+"_"+y);
	if(x<0||x>=map_x||y<0||y>=map_y)return;
	mine[x][y]=1;
	map[x][y]++;
	if(y+1<map_y)map[x][y+1]++;
	if(y-1>=0)map[x][y-1]++;
	yy=(x%2==1)-(x%2==0);
	if(x+1<map_x){
		map[x+1][y]++;
		if(0<=y+yy<map_y)map[x+1][y+yy]++;
	}
	if(x-1>=0){
		map[x-1][y]++;
		if(0<=y+yy<map_y)map[x-1][y+yy]++;
	}
}
function deploySub(x,y){//マイン消去
	console.log("deploySub:"+x+"_"+y);
	if(x<0||x>=map_x||y<0||y>=map_y)return;
	if(mine[x][y]==0)return;
	mine[x][y]=0;
	map[x][y]--;
	if(y+1<map_y)map[x][y+1]--;
	if(y-1>=0)map[x][y-1]--;
	yy=(x%2==1)-(x%2==0);
	if(x+1<map_x){
		map[x+1][y]--;
		if(0<=y+yy<map_y)map[x+1][y+yy]--;
	}
	if(x-1>=0){
		map[x-1][y]--;
		if(0<=y+yy<map_y)map[x-1][y+yy]--;
	}
}
function getRandomInt(max){//乱数
	return Math.floor(Math.random() * max);
}
function deploy_main(){//マイン生成メイン
	for(var i=0;i<mine_max;i++){
		x=getRandomInt(map_x);
		y=getRandomInt(map_y);
		if(mine[x][y]==1)i--;
		if(mine[x][y]==0)deploy(x,y);
	}
}
function startPoint(x,y){//スタート位置のマイン除去
	console.log("startPoint:"+x+"_"+y);
	if(mine[x][y]==1){
		deploySub(x,y);
		for(var i=0;i<1;i++){
			xx=getRandomInt(map_x);
			yy=getRandomInt(map_y);
			if(mine[x][y]==0&&(x!=xx||y!=yy))deploy(xx,yy);
			else i--;
		}
	}
	disp();
}
function hexLock(x,y){//ヘクスロック
	console.log("hexLock:"+x+"_"+y);
	if(isGameOver!=0)return false;
	if(prog[x][y]==2){
		prog[x][y]=0;
		document.getElementById(x+"_"+y).src="hex.png";
	}else
	if(prog[x][y]==0){
		prog[x][y]=2;
		document.getElementById(x+"_"+y).src="hexFlag.png";
	}
	return false;
}
function hexOpen(x,y){//ヘクスオープン
	console.log("hexOpen:"+x+"_"+y);
	if(isGameOver!=0)return;
	if(prog[x][y]!=0)return;
	if(open_num==0&&mine[x][y]==1)startPoint(x,y);
	prog[x][y]=1;
	if(mine[x][y]==1){
		document.getElementById(x+"_"+y).src="hexOut.png";
		isGameOver=1;
		document.getElementById("GameClear").style.display="";
		document.getElementById("GameClear").innerHTML="作戦続行不可";
	}else{
		if(map[x][y]==0)document.getElementById(x+"_"+y).src="hexOpen.png";
		else document.getElementById(x+"_"+y).src="hexOpenSide.png";
		if(map[x][y]!=0)document.getElementById("t"+x+"_"+y).innerHTML=map[x][y];
		open_num++;
	}
	if(map[x][y]==0){
		if(y+1<map_y)hexOpen(x,y+1);
		if(y-1>=0)hexOpen(x,y-1);
		let yy=(x%2==1)-(x%2==0);
		if(x+1<map_x){
			hexOpen(x+1,y);
			if(0<=y+yy<map_y)hexOpen(x+1,y+yy);
		}
		if(x-1>=0){
			hexOpen(x-1,y);
			if(0<=y+yy<map_y)hexOpen(x-1,y+yy);
		}
	}
	isGameClear();
	updateInfo();
}
function isGameClear(){
	if(open_num>=map_x*map_y-mine_max){
		isGameOver=2;
		document.getElementById("GameClear").style.display="";
		document.getElementById("GameClear").innerHTML="作戦終了";
	}
}
function updateInfo(){//情報更新
	info="残りヘクス:"+(map_x*map_y-open_num)+" マイン合計:"+mine_max;
	document.getElementById("info").innerHTML=info;
}
function Retry(){//リトライ
	init();
	deploy_main();
	disp();
}
//----------
Retry();
//----------
</script>

</html>
