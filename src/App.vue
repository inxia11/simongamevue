<template>
  <div id="app">
    <div class="title">Simon says</div>
    <div class="game">
      <div class="gameTable" >
        <div id="red" class="quarter" @[capture]="captureTap('red')" :class="{ 'bright': currentButton === 'red' }"></div>
        <div id="blue" class="quarter" @[capture]="captureTap('blue')" :class="{ 'bright': currentButton === 'blue' }"></div>
        <div id="green" class="quarter" @[capture]="captureTap('green')" :class="{ 'bright': currentButton === 'green' }"></div>
        <div id="yellow" class="quarter" @[capture]="captureTap('yellow')" :class="{ 'bright': currentButton === 'yellow' }"></div>
      </div>
      <div class="gameOption">
        <div class="rounCount">Раунд: {{round}}</div>
        <button class="start" @[starting]="start()">старт</button>
        
        <div class="optionTitle">Уровни сложности</div>
        <input type="radio" name="complex" v-model="complex" value="easy">Легкий <br>
        <input type="radio" name="complex" v-model="complex" value="normal">Средний <br>
        <input type="radio" name="complex" v-model="complex" value="heavy">Сложный <br>
      </div>
      <audio :key="'sound' + index" v-for="(sound, index) in sounds" :ref="'sound' + index">
            <source :src="sound" type="audio/mpeg">
      </audio>
    </div>
    <div class="gameLose" v-if="lose">простите но вы проиграли на {{round}} уровне</div>
  </div>
</template>

<script>

export default {
  
  data() {
    return {
      longest: 0,
      complex: 'easy',
      lose: false,
      round: 0,
      sequence: [],
      taps: [],
      playSequenceId: null,
      currentButton: '',
      playSequenceCounter: 0,
      capture: '',
      starting: 'click',
      lights: [ 'red', 'blue', 'green', 'yellow' ],
      sounds: { 'red': 'https://s3.amazonaws.com/freecodecamp/simonSound1.mp3', 
                'blue': 'https://s3.amazonaws.com/freecodecamp/simonSound2.mp3', 
                'green': 'https://s3.amazonaws.com/freecodecamp/simonSound3.mp3', 
                'yellow': 'https://s3.amazonaws.com/freecodecamp/simonSound4.mp3'}
        
    }

  },




  methods: {
    start () {
			this.sequence = [];
			this.taps = [];
      this.round = 0;
			this.playSequenceCounter = 0;
      this.lose = false;
      this.capture = 'disable';
      this.starting = 'disable';
      this.playSequence();
    },
    
    playSound (id) {
      this.$refs['sound' + id][0].play()
    },
    
    addToSequence () {
			var index = Math.floor(Math.random() * 4);
			this.sequence.push(this.lights[index]);
    },
    
    captureTap(color) {
        this.starting = 'click';
				this.currentButton = color;
				var self = this;
				setTimeout(function() 
				{
          self.currentButton = '';
          self.playSound(color);
          
				}, 300);

				var last_index = this.taps.length;
        this.taps.push(color);
        
				if (color === this.sequence[last_index]) 
				{					
					if (this.taps.length === this.sequence.length) 
					{
						this.taps = [];
						
						this.round = this.sequence.length;
						if (this.longest < this.sequence.length) 
						{
							this.longest = this.sequence.length;
						}
						setTimeout(function() 
						{
              self.playSequence();
              self.capture = 'disable';
						}, 1000);
					}
				}
				else {
          this.gameOver();
          this.starting = 'click';
				}
			
    },

    playSequence() 
		{
      
			var self = this;
      self.addToSequence();
      this.starting = 'disable';
      
      if (this.complex == 'easy') {
        
		
        self.playSequenceId = window.setInterval(function() 
        {
          self.currentButton = self.sequence[self.playSequenceCounter];
          self.playSound(self.currentButton)
          setTimeout(function() 
          {
            self.currentButton = '';
            self.playSequenceCounter++;
            if (self.playSequenceCounter === self.sequence.length) 
            {
              window.clearInterval(self.playSequenceId);
              self.playSequenceCounter = 0;
              self.capture = 'click';
              
            }
          }, 200);
        }, 1500);
      } else if (this.complex == 'normal') {
          self.playSequenceId = window.setInterval(function() 
          {
            self.currentButton = self.sequence[self.playSequenceCounter];
            self.playSound(self.currentButton)
            setTimeout(function() 
            {
              self.currentButton = '';
              self.playSequenceCounter++;
              if (self.playSequenceCounter === self.sequence.length) 
              {
                window.clearInterval(self.playSequenceId);
                self.playSequenceCounter = 0;
                self.capture = 'click';
              
              }
            }, 200);
          }, 1000);
      } else if (this.complex == 'heavy') {
        self.playSequenceId = window.setInterval(function() 
          {
            self.currentButton = self.sequence[self.playSequenceCounter];
            self.playSound(self.currentButton)
            setTimeout(function() 
            {
              self.currentButton = '';
              self.playSequenceCounter++;
              if (self.playSequenceCounter === self.sequence.length) 
              {
                window.clearInterval(self.playSequenceId);
                self.playSequenceCounter = 0;
                self.capture = 'click';
              }
            }, 100);
          }, 400);
      }
		},
    
    gameOver() 
		{
      this.lose = true;
      this.capture = 'disable';
      
    },
    
    


  }


}

</script>

<style lang="sass">

  #app
  
    width: 100%

    .title
      width: 100%
      font-size: 40px
      text-align: center
      margin-top: 50px
      margin-bottom: 50px

    .game
      display: flex
      width: 70%
      margin: auto
      justify-content: center

      .gameTable
        width: 400px
        height: 400px
        display: flex
        flex-wrap: wrap

      


        .quarter
          border: 0.5px solid black
          width: 49%
          height: 50%
          transition: background-color 0.1s ease-in-out

          &:hover
            border: 0.5px solid white

        

        #red 
          border-radius: 200px 0 0 0
          background: red
          opacity: 0.5


        #blue 
          border-radius: 0 200px  0 0
          background: blue
          opacity: .5

        #green
          border-radius: 0 0 0 200px  
          background: green
          opacity: 0.5

        #yellow 
          border-radius: 0 0 200px 0
          background: orange
          opacity: 0.5

      

  .bright 
    opacity: 1 !important

  .gameOption
    margin-left: 50px


  .rounCount
    font-size: 30px
    margin-bottom: 20px

  .start
    width: 100px
    height: 50px
    background-color: rgb(146, 103, 200)
    border: 2px solid blue
    border-radius: 10px
    font-size: 15px
    margin: auto 10px

    &:active
      border: none !important

    
  .gameLose
    font-size: 30px
    color: coral
    text-align: center
    margin-top: 50px

  .optionTitle
    margin: 10px auto 10px auto 







          

        




</style>
