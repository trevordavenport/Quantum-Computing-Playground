Quantum-Computing-Playground
============================
> Contributors: Trevor Davenport, Brian Freese

#### General Overview ####
```
 Building off of QScript (quantumplayground.net), we will be making additions and 
 modifications to the existing source code. This is the final project for 
 UC Berkeley's CS164 Compilers Course.

```

#### Demo
[![](http://img.youtube.com/vi/kVZbwPeSO_E/0.jpg)](http://www.youtube.com/watch?v=kVZbwPeSO_E)

#### Implementation & Additions to compiler.js

```javascript

//*****************************************************************************************************//
  //************************************ START OF CS164 FINAL PROJECT CODE ******************************//
  //*****************************************************************************************************//

  //hacky fix for editor cutting for loops short::: in stepRun_ (in client.js when running in browser)
      // if (this.editor_.lineInfo(cline) != null) {
      //   this.editor_.lineInfo(cline).gutterMarkers && (this.runmode_ = !1);
      // } else {
      //   break;
      // }



  //first, we need to make sure we can use underscore for flattening the program array after adding new lines as an array
  var underscore164 = document.createElement( "script" );
  underscore164.type = "text/javascript";
  underscore164.src = "http://documentcloud.github.com/underscore/underscore.js";
  $("body").append(underscore164);


  var cs164VectorSize

  function cs164BraKetDesugar(script){
    var lineNum = 0

    while (lineNum < script.length) {
      line = script[lineNum]
      desugaredLine = desugarLine(prepLine(line))
      script[lineNum] = desugaredLine

      lineNum++
    }
    return _.flatten(script)
  }

  function prepLine(l) {
    if (l == null) {
      return ''
    }
    var leadingWhiteSpace = /(\|\s*)/
    var trailingWhiteSpace = /(\s*\>)/
    var preCommaWhite = /(\s+\,)/
    var postCommaWhite = /(\,\s+)/

    var tem = l
    var tem1 = 0
    while (tem != tem1) {
      tem1 = tem.replace(preCommaWhite, ',')
      tem = tem1.replace(postCommaWhite, ',')
    }

    var pl = tem.replace(leadingWhiteSpace, '|').replace(trailingWhiteSpace, '>')
    return pl
  }

  function desugarLine(l) {
    if (l == null) {
      return ''
    }
    if (l.substr(0,2) == '//'){
      return l
    }
    
    var newLine

    var vectorSizeRX = /\s*VectorSize\s*([0-9]+)/
    var vectorSizeMatch = vectorSizeRX.exec(l)
    if (vectorSizeMatch != null) {
      cs164VectorSize = vectorSizeMatch[1]
      return l
    }



    var singleKet = /\|(\w+)\>/
    var singleMatch = singleKet.exec(l)
    if (singleMatch != null) {
      //for single kets, we just need to replace the ket with no ket
      newLine = l.replace(singleMatch[0], singleMatch[1])
      return newLine
    }
    var multiKet = /\|((?:\w+\,)+)(\w+)\>/
    var multiMatch = multiKet.exec(l)
    if (multiMatch != null) {
      //for multiple kets, we need to turn one line into multiple
      //with the same gate application
      //which means we need to adjust the this.text object in QScript source!
      //should be able to use _ to replace text[line] = newLine (which could be multiple lines), and then flatten the text object
    
      var gate = l.substr(0, multiMatch.index)
      var indexStr = multiMatch[1] + multiMatch[2]
      var indexArr = indexStr.split(',')

      newLine = _.map(indexArr, function(q){ return (gate + q)})

      return newLine

    }
    var filterKet = /\|filter\((\w+)\)\{(.+)*\}\>/
    var filterMatch = filterKet.exec(l)
    if (filterMatch != null) {

      var gate = l.substr(0, filterMatch.index)
    
      var cs164IndexVar = filterMatch[1]
      var cs164ConditionB = filterMatch[2].replace('return', '')
      //var newLines = ['for indexVar = 0; indexVar < vectorSize...', 'if'+conditionB, gate + indexVar, 'endif', 'endfor']
      newLine = []
      newLine.push('for '+ cs164IndexVar+' = 0; '+cs164IndexVar+' < ' + cs164VectorSize+'; '+cs164IndexVar+'++')
      newLine.push('if ' + cs164ConditionB)
      newLine.push(gate + cs164IndexVar)
      newLine.push('endif')
      newLine.push('endfor')
      return newLine
    }

    var gateRX = /Gate\s+(\w+)\s*\=\s*\[\s*((?:[-.0-9]+\,){7})([-.0-9]+)\s*\]/
    var gateMatch = gateRX.exec(l)
    if (gateMatch != null) {

      //get gate name
      var gateName = gateMatch[1]
      //reassemble matrix
      var gateMatrix = gateMatch[2]+gateMatch[3]
      newLine = ['proc ' + gateName + ' q']
      newLine.push('Unitary q, ' + gateMatrix)
      newLine.push('endproc')
      //newLine = [Proc gateName q, Unitary q matrix, endproc ]
      return newLine

    } 
    return l

  }

  

  //call the braket desugaring function
  this.text_ = cs164BraKetDesugar(this.text_);


  //*****************************************************************************************************//
  //************************************ END OF CS164 FINAL PROJECT CODE ********************************//
  //*****************************************************************************************************//

```


#### Final Presentation
![](http://i.imgur.com/Of76n0wl.png)
![](http://i.imgur.com/OxfYCB6l.png)
![](http://i.imgur.com/IMu1EgLl.png)
![](http://i.imgur.com/4NorsZ7l.png)
![](http://i.imgur.com/1ECHOhal.png)
![](http://i.imgur.com/aTxNFzRl.png)




#### Modifications & Optimizations ####
```
 1) Desugarging of Bra-Kets notation
 2) Creating Gate Variable using Macros (SweetenJS)
 3) Implement functionality of a 'quantum' Lambda

```

