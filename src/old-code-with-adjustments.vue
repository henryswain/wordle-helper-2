<script setup>
  // Import necessary modules
  import { ref, onMounted } from "vue";
  import papaparse, { BYTE_ORDER_MARK } from 'papaparse';

  // Define reactive variables
  const remaining_words = ref([]);
  const remaining_words2 = ref([]);
  const wordAttempt = ref('');
  const wordResult = ref('');
  const loaded = ref(false)
  const isLoading = ref(false)
  const wordGuesses = ref([])
  const wordResults = ref([])
  const letter1 = ref(false)
  const letter2 = ref(false)
  const letter3 = ref(false)
  const letter4 = ref(false)
  const letter5 = ref(false)
  const color1 = ref(false)
  const color2 = ref(false)
  const color3 = ref(false)
  const color4 = ref(false)
  const color5 = ref(false)
  
  // File path to your CSV file
  const dataFile = "/assets/sgb-words.txt"


  const fileLines = ref([]);

// Fetch the file when the component is mounted
onMounted(async () => {
  try {
    const response = await fetch(dataFile);
    if (!response.ok) {
      throw new Error(`Failed to fetch file: ${response.statusText}`);
    }
    const text = await response.text();
    fileLines.value = text.split('\n').map(line => line.trim());
    console.log(fileLines.value)
    showData(fileLines.value)
  } catch (error) {
    console.error('Error fetching and parsing file:', error);
  }
});
  // Function to load data from CSV file
//   onMounted(() => {
//     isLoading.value = true;
//     fetch(dataFile)
// .then(res => {
//       console.log(res.toString().split("/n"))
//     return showData(res.toString().split("/n"));
//   });
// })
        // .then((res) => res.text())
      // .then((text) => loadData(text))
      // .then((parsedText) => showData(parsedText));

  // Function to load data from CSV file
  // async function loadData(dataText) {

    // let data = papaparse.parse(dataText, { delimiter: ",", header: true });
  //   console.log(dataText.split("/n"))
  //   return dataText;
  // }


  function showData(parsedData) {
    console.log('showData called')
    for (let wordDict of parsedData) {
      if (wordDict.length === 5) {

        remaining_words.value.push(wordDict.toLowerCase())
        console.log('remaining_words.value: ', remaining_words.value)
        // remaining_words.value.push(wordDict["word"].toLowerCase())
        //remaining_words.value = remaining_words.value.sort()
        // remaining_words2.value.push(wordDict["word"].toLowerCase())
        //remaining_words2.value = remaining_words.value.sort()
      }
    }
    remaining_words.value = remaining_words.value.sort()
    remaining_words2.value = remaining_words.value.sort()

    loaded.value = true
    isLoading.value = false

  }

  function resetWords() {
    letter1.value = false
    letter2.value = false
    letter3.value = false
    letter4.value = false
    letter5.value = false
    remaining_words.value = remaining_words2.value
    wordGuesses.value = []
    wordResults.value = []
  }
  // Function to filter and update the list of remaining words
  function testFunction() {
    console.log("testFunction Called:")
  }
  const addWordInfo = () => {

    console.log("addWordInfo Called:")
    wordResult.value = letter1.value + letter2.value + letter3.value + letter4.value + letter5.value
    wordGuesses.value.push(wordAttempt.value)


    wordResults.value.push(wordResult.value)

    isLoading.value = true;
    loaded.value = false;
    
    setTimeout(() => {
    
    // console.log("wordGuesses.value: ", wordGuesses.value)
    // console.log(wordGuesses.value)
    // wordGuesses.value = wordGuesses.value.push(wordAttempt.value)

    // console.log("wordResults.value: ", wordResults.value)
    // wordResults.value = wordResults.value.push(wordResult.value)
    let c = 0
    //const filteredWords = remaining_words.value.filter(word => {
    remaining_words.value = remaining_words.value.filter((word) => {

      

      for (let m = 0; m < wordAttempt.value.length; m++) {
        // creates an array of the indicies of green characters
        let color = wordResult.value[m].toLowerCase(); 
        let letter = wordAttempt.value[m].toLowerCase();

        
        if (color === 'g' ) {
          if (word.charAt(m) !== letter) {
            return false;
          }
        }
      }
      return true
    })
  


    remaining_words.value = remaining_words.value.filter((word) => {
      let truths = [];
      let nongreenindex = []
      for (let l in wordResult.value) {
        if (wordResult.value.charAt(l) !== "g") {
          nongreenindex.push(l)
        }
      } 
 
      // creates a map of all black characters and their black index
      const blackExist = ref({})
      for (let i = 0; i < wordResult.value.length; i++) {
        if (wordResult.value.charAt(i).toLowerCase() === "b") {

          if (wordAttempt.value.charAt(i) in blackExist.value) {
            blackExist.value[wordAttempt.value.charAt(i)].push(i)
          }

          else {blackExist.value[wordAttempt.value.charAt(i)] = [i]}
        }
      }
      console.log("blackExist.value: ", blackExist.value)



      // creates a map of all yellow characters and their yellow indexes
      const ylettmap = ref({})
      for (let i = 0; i < wordResult.value.length; i++) {
        if (wordResult.value.charAt(i).toLowerCase() === "y") {
          //if (ylettmap.value.hasOwnProperty(wordAttempt.value.charAt(i).toLowerCase())) {
          if (wordAttempt.value.charAt(i) in ylettmap.value) {

            ylettmap.value[wordAttempt.value.charAt(i)].push(i)
          }
        
        else {ylettmap.value[wordAttempt.value.charAt(i)] = [i]}
        }
      }

      console.log("ylettmap.value: ", ylettmap.value)

      // creates a map of all non-green characters and their nongreen indexes
      const charExist = ref({})
      for (let i = 0; i < wordResult.value.length; i++) {
        if (wordResult.value.charAt(i).toLowerCase() === "y" || wordResult.value.charAt(i).toLowerCase() === "b") {
     
          if (wordAttempt.value.charAt(i) in charExist.value) {
            charExist.value[wordAttempt.value.charAt(i)].push(i)
          }
        
          else {charExist.value[wordAttempt.value.charAt(i)] = [i]}
        }
      }

      console.log("charExist.value: ", charExist.value)
      for (let i = 0; i < wordAttempt.value.length; i++) {


        let color = wordResult.value[i].toLowerCase();
        let letter = wordAttempt.value[i].toLowerCase();


        if (color === "c") {


          let count = 0;
          let alreadyCovered = [];
          let bad = false;



          console.log("ylettmap.value: ", ylettmap.value)
          if (letter in ylettmap.value) {

            let possibleExistancePlaces = ref([])
            for (let i of nongreenindex) {
              if (!(charExist.value[letter].includes(i))) {
                possibleExistancePlaces.value.push(parseInt(i))
              }
            }

            for (let i in word) {
              if (!(possibleExistancePlaces.value.includes(i))) {
                if (word[i] == letter) {
                  truths.push(false)
                  bad = true
                  break
                }
              }
              else {
                if (word[i] == letter) {
                  count++ 
                }
              }
            }
            if (bad == false) {
              if (count == ylettmap.value[letter].length) {
                truths.push(true) 
              }
              else {
                truths.push(false)
              }
            }
          }

          /* this is old code */


          //   for (let j = 0; j < wordAttempt.value.length; j++) {
          //     j = j.toString()
          //     if (nongreenindex.includes(j)) {

 
          //       if (charExist.value[letter].includes(j)) {
          //         if (word[parseInt(j)] === letter) {
    
          //           console.log("inside if statement")
          //           bad = true;
          //           truths.push(false);
          //           break;
          //         }
          //       }
    
          //       if ((letter === word[parseInt(j)]) && (!(alreadyCovered.includes(j)))) {
          //         alreadyCovered.push(j)
          //         count += 1;
          //      }
          //     } 
          //   }
          //   if (bad === false) {
          //     if (count === ylettmap.value[letter].length) {
          //       truths.push(true)
        
          //     }

          //      else {truths.push(false);}
          //   }

          // }

          else {
            let bad2 = false
            for (let i of nongreenindex) {
              if (word[i] == letter) {
                truths.push(false)
                bad = true
              }
            }
            if (!bad2) {
              return true
            }
          }

          /* thisi is old code */


          // else {

          //   let conditionmet = false;
          //   for (let i = 0; i < wordAttempt.value.length; i++) {
          //     i = i.toString()
          //     if (nongreenindex.includes(i)) {
    
                

          //       if (word[i] == letter) {
          //         conditionmet = true;
          //         truths.push(false)
              
          //       }
          //     }
          //   }
          //   if (!conditionmet) {
          //     truths.push(true)
          //   }
          // }
          
        
        }


  
        if (color === "y") {

          let count = 0;
          let alreadyCovered = [];
          let bad = false;

          console.log("blackExist.value: ", blackExist.value)
          if (letter in blackExist.value) {

            let possibleExistancePlaces = ref([])
            for (let i of nongreenindex) {
              if (!(charExist.value[letter].includes(i))) {
                possibleExistancePlaces.value.push(parseInt(i))
              }
            }

            for (let i in word) {
              if (!(possibleExistancePlaces.value.includes(i))) {
                if (word[i] == letter) {
                  truths.push(false)
                  bad = true
                  break
                }
              }
              else {
                if (word[i] == letter) {
                  count++ 
                }
              }
            }
            if (bad == false) {
              if (count == ylettmap.value[letter].length) {
                truths.push(true) 
              }
              else {
                truths.push(false)
              }
            }
          }


            /*this is old code*/

            // for (let j = 0; j < wordAttempt.value.length; j++) {

            //   j = j.toString()
            //   if (nongreenindex.includes(j)) {
            //     if (charExist.value[letter].includes(j)) {
            //       if (word[parseInt(j)] === letter) {
    
            //         bad = true;
            //         truths.push(false);
            //         break;
            //       }
            //     }
    
            //     if ((letter === word[parseInt(j)]) && (!(alreadyCovered.includes(j)))) {
            //       alreadyCovered.push(j)
            //       count += 1;
            //     }
            //   } 
            // }
            // if (bad === false) {
            //   if (count === ylettmap.value[letter].length) {
            //     truths.push(true)
        
            //   }
            //   else {truths.push(false);}
            // }

          // }
          else {
            let good = false
            for (let i of nongreenindex) {
              if (word[i] == letter) {
                truths.push(true)
                good = true
                break
              }
            }
            if (good == false) {
              truths.push(false)
            }

            /*this is old code*/
      
          //   let conditionmet = false;


          //   for (let i = 0; i < wordAttempt.value.length; i++) {
          //     i = i.toString()

          //     if (nongreenindex.includes(i)) {

          //       if (ylettmap.value[letter].includes(parseInt(i))) {

          //         if (word[i] == letter) {
          //           conditionmet = false;
          //           break
          //         }
          //       }

               
          //       else {

          //         if (word[i] == letter) {
          //           conditionmet = true;
                    
          //         }
          //       }

          //     }
          //   }
          //   if (conditionmet == true) {
          //     truths.push(true);
          //   }
          //   else {
          //     truths.push(false);
          //   }

          // }
          }
        }
      }
      if (truths.includes(false)) {
        return false;
      }
      else {return true;}
    })
    loaded.value = true; // Set loaded to true after filtering
    isLoading.value = false; // Set isLoading to false after filtering


    // Clear input fields
    wordAttempt.value = '';
    wordResult.value = '';
  }, 0);
  letter1.value = false
  letter2.value = false
  letter3.value = false
  letter4.value = false
  letter5.value = false
}
</script>

<template>
  <div class="my-3 mx-3">
    <header>
      <h2>Enter information below for an updated list of possible wordle words.</h2>
    </header>
  </div>

  <div class="mb-1 mx-3">
    <label for="word" class="form-label">From the list below, enter the word you used for your wordle guess.</label>
    <input type="text" class="form-control" id="word" ref="fValue" v-model="wordAttempt" placeholder="paste it here">
  </div>



  <div class="mb-1 mx-3">
    <p>Enter wordles feedback for you by selecting the appropriate colors in the dropdown buttons below.</p>
    <!-- <label for="wordColorKey" class="form-label">For each letter of your word attempt, type the letter corresponding to color feedback: y for yellow, g for green, c for charcoal.</label>
    <input type="text" class="form-control" id="WordColorKey" v-model="wordResult" placeholder="type code here. Ex: cycgg"> -->
  </div>

  <div class="mb-1 mx-3">
    <div class="btn-group" role="group" aria-label="Button group with nested dropdown">

      <div v-if="letter1 === false">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-primary dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            1st letter color
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter1 = false">1st letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter1 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter1 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter1 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else-if="letter1 === 'c'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-secondary dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            grey    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter1 = false">1st letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter1 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter1 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter1 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else-if="letter1 === 'y'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-warning dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            yellow    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter1 = false">1st letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter1 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter1 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter1 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else="letter1 === 'g'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-success dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            green    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter1 = false">1st letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter1 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter1 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter1 = 'g'">green</button></a>

          </div>
        </div>
      </div>




      <div v-if="letter2 === false">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-primary dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            2nd letter color
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter2 = false">2nd letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter2 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter2 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter2 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else-if="letter2 === 'c'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-secondary dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            grey    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter2 = false">2nd letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter2 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter2 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter2 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else-if="letter2 === 'y'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-warning dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            yellow    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter2 = false">2nd letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter2 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter2 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter2 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else="letter2 === 'g'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-success dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            green    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter2 = false">2nd letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter2 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter2 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter2 = 'g'">green</button></a>

          </div>
        </div>
      </div>





      <div v-if="letter3 === false">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-primary dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            3rd letter color
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter3 = false">3rd letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter3 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter3 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter3 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else-if="letter3 === 'c'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-secondary dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            grey    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter3 = false">3rd letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter3 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter3 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter3 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else-if="letter3 === 'y'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-warning dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            yellow    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter3 = false">3rd letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter3 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter3 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter3 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else="letter3 === 'g'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-success dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            green    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter3 = false">3rd letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter3 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter3 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter3 = 'g'">green</button></a>

          </div>
        </div>
      </div>




      <div v-if="letter4 === false">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-primary dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            4th letter color
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter4 = false">4th letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter4 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter4 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter4 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else-if="letter4 === 'c'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-secondary dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            grey    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter4 = false">4th letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter4 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter4 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter4 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else-if="letter4 === 'y'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-warning dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            yellow    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter4 = false">4th letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter4 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter4 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter4 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else="letter4 === 'g'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-success dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            green    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter4 = false">4th letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter4 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter4 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter4 = 'g'">green</button></a>

          </div>
        </div>
      </div>



      <div v-if="letter5 === false">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-primary dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            5th letter color
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter5 = false">5th letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter5 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter5 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter5 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else-if="letter5 === 'c'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-secondary dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            grey    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter5 = false">5th letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter5 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter5 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter5 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else-if="letter5 === 'y'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-warning dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            yellow    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter5 = false">5th letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter5 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter5 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter5 = 'g'">green</button></a>

          </div>
        </div>
      </div>
      <div v-else="letter5 === 'g'">
        <div class="btn-group" role="group">
          <button id="btnGroupDrop1" type="button" class="btn btn-success dropdown-toggle" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            green    
          </button>
          <div class="dropdown-menu" aria-labelledby="btnGroupDrop1">
            <!-- <button type="button" class="btn btn-secondary">grey</button>
            <button type="button" class="btn btn-warning">yellow</button>
            <button type="button" class="btn btn-success">green</button> -->
            <a class="dropdown-item"><button type="button" class="btn btn-primary" @click="letter5 = false">5th letter color</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-secondary" @click="letter5 = 'c'">grey</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-warning" @click="letter5 = 'y'">yellow</button></a>
            <a class="dropdown-item"><button type="button" class="btn btn-success" @click="letter5 = 'g'">green</button></a>

          </div>
        </div>
      </div>
    </div>
  </div>


  <div class="row">
    <div class="col-3">
      <div class="my-3 mx-3">
        <button type="button" class="btn btn-primary" @click="addWordInfo()">add Word information</button>
      </div>
    </div>


    <div class="col-2">
      <div class="my-3 mx-3">
        <button type="button" class="btn btn-primary" @click="resetWords()">reset words</button>
      </div>
    </div>
  </div>


  <div class="mx-1 mb-1">
    <hr>
  </div>


  <div class="row mx-1 mb-1">
    <h5>Word attempts and results so far.</h5>


    <div col-1>
    <!-- <div v-for="(result, index) in wordResults" :key="index"> -->
          <!-- {{ index + 1}}. {{ wordGuesses[index] }} {{ result }} -->
      <div v-for="(result, index) in wordResults" :key="index">
        {{ index + 1}}. {{ wordGuesses[index] }} {{  }}
      </div>
    </div>
  </div>


  <div class="mx-1 mb-1">
    <hr>
  </div>



  <div class="mx-3" v-if="loaded">
    <div v-for="word in remaining_words" :key="word">{{ word }}</div>
  </div>


  <div class="mx-3" v-else-if="isLoading">
      <h1>Loading Words...</h1>
  </div>
</template>

