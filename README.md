
<!DOCTYPE html>
<html>
  <head>
    <title>Eric Li</title>
    <link rel="icon" type="image/png" href="img/favicon.png">
    <link rel="stylesheet" href="index.css">
    <link href="https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;600&display=swap" rel="stylesheet">
    <link href="prism.css" rel="stylesheet">
  </head>
  <body>
    <div>
      <img src="img/profile.jpg">
    </div>
    <div class="container">
      <h1>Student of the Paul G. Allen School of CS at the University of Washington</h1>
      <div class="flex">
        <div class="about">
          <h2>About</h2>
          <p>
            Hey there, my name is Eric! I am part of the University of Toronto class of 2025 and I'm interested in web development,
            algorithms, and basketball.
          </p>
        </div>
        <div class="contact">
          <h2>Contact</h2>
          <p>Kenneth</p>
          <p>Lee</p>
          <a href="https://www.linkedin.com/in/kenneth-lee-5141781a5/" target="_blank">LinkedIn</a>
        </div>
      </div>
    </div>
    <div class="project">
      <h3>Favorite Java project</h3>
      <p>
        One of my favorite projects thus far came from my Computer Programming II
        class at UW. The premise of the assignment, Huffman Coding, is learning how
        to compress data through the creation of a Huffman tree
        that is used for encoding. The topic of data compression was interesting, but
        I also realized the importance of recursion with binary tree creation and
        traversal.
      </p>
      <p>
        My code for the project is shown below. I utilized the <a href="https://prismjs.com/" target="_blank">PrismJS</a> syntax highlighter
        and CSS styling for displaying the code.
      </p>
    </div>
    <div class="code-container">
      <pre>
        <code class="language-java">
          // Kenneth Lee
          // Created on 6/4/2020
          //
          // This class helps create a Huffman code that can be used
          // to compress and read files with ASCII characters. Enjoy!

          import java.util.*;
          import java.io.*;

          public class HuffmanCode {
            // overall huffman tree
            private HuffmanNode overallRoot;
            
            // post = creates a new HuffmanCode object with the given
            // array of frequencies. The index of the frequency in the array stores
            // the ASCII character it is, while the correlating values store the frequencies
            // of each specified character.
            //
            // parameters:
            //    int[] frequencies = array of frequencies used to construct
            //                        HuffmanCode object
            public HuffmanCode(int[] frequencies) {
                Queue<HuffmanNode> chars = new PriorityQueue<HuffmanNode>();
                for (int i = 0; i < frequencies.length; i++) {
                  int charFrequency = frequencies[i];
                  if (charFrequency > 0) {
                      HuffmanNode newChar = new HuffmanNode((char) i, charFrequency);
                      chars.add(newChar);
                  }
                }
              
                while (chars.size() > 1) {
                  HuffmanNode left = chars.remove();
                  HuffmanNode right = chars.remove();
                  int rootFrequency = left.frequency + right.frequency;
                  HuffmanNode root = new HuffmanNode((char) 0, rootFrequency, left, right);
                  chars.add(root);
                }
              
                overallRoot = chars.remove();
            }
            
            // post = creates a new HuffmanCode object from the given Scanner
            // object, using the encoded paths and ASCII values to create the object. The paths
            // and ASCII values are in 2-line pairs, with the first pair in each line containing
            // the ASCII value and the second line containing the encoded path.
            //
            // parameters:
            //    Scanner input = Scanner object used to construct a new HuffmanCode
            //                    object. The object is assumed to not be null
            //                    and always contains data encoded in the standard format.
            public HuffmanCode(Scanner input) {
                while (input.hasNextLine()) {
                  int asciiValue = Integer.parseInt(input.nextLine());
                  String path = input.nextLine();
                  
                  overallRoot = createHuffman(asciiValue, path, overallRoot);
                }
            }
            
            // A private method that is part of the public-private recursive pairing with the
            // HuffmanCode(Scanner input) method. This method traverses through the current overallRoot
            // recursively until reaching a leaf node, in which new HuffmanNodes are continuously created
            // unitl reaching the new leaf node with the given ascii character from the input.
            private HuffmanNode createHuffman(int asciiValue, String path, HuffmanNode root) {
                if (path.isEmpty()) {
                  return new HuffmanNode((char) asciiValue, 0);
                }
              
                if (root == null) {
                  root = new HuffmanNode((char) 0, 0);
                }
              
                if (path.charAt(0) == '0') {
                  root.left = createHuffman(asciiValue, path.substring(1), root.left);
                } else {
                  root.right = createHuffman(asciiValue, path.substring(1), root.right);
                }
              
                return root;
            }
            
            // post = Writes the current Huffman codes to the given PrintStream object,
            // in standard format (2-line pairs, first line in each pair contains ASCII
            // value of character, second line in each pair contains encoded path to the character).
            //
            // parameters:
            //    PrintStream output = a given output that is written to for storing the current
            //                         Huffman codes
            public void save(PrintStream output) {
                save(overallRoot, output, "");
            }
            
            // A private method in the public-private recursive pairing with the save()
            // method. Recursively creates a path to each character in the HuffmanCode tree,
            // and printing the ASCII value of the character and created path once reaching
            // the leaf nodes for each character.
            private void save(HuffmanNode root, PrintStream output, String path) {
                if (root.left == null && root.right == null) {
                  output.println((int) root.character);
                  output.println(path);
                } else {
                  save(root.left, output, path + "0");
                  save(root.right, output, path + "1");
                }
            }
            
            // post = reads the individual bits from the given input, goes through the
            // Huffman codes based off the bit information, and writing the characters
            // to the given PrintStream object. This process repeats until the given input
            // is empty.
            //
            // parameters:
            //    BitInputStream input = object that contains the bit information needed
            //                           to produce characters from the Huffman code and write
            //                           to the given PrintStream object. The input is assumed
            //                           to be compatible with the current HuffmanCode object
            //    PrintStream output = a given output that is written to for storing the characters
            public void translate(BitInputStream input, PrintStream output) {
                while (input.hasNextBit()) {
                  translate(overallRoot, input, output);
                }
            }
            
            // A private method in the public-private recursive pairing with the translate() method.
            // The method recursively traverses the Huffman tree until reaching a leaf node with
            // a stored character, which then writes the node's character to the output.
            private void translate(HuffmanNode root, BitInputStream input, PrintStream output) {
                if (root.left == null && root.right == null) {
                  output.write(root.character);
                } else {
                  int nextBit = input.nextBit();
                    
                  if (nextBit == 0) {
                      translate(root.left, input, output);
                  } else {
                      translate(root.right, input, output);
                  }
                }
            }
            
            // The HuffmanNode class used to create the HuffmanCode object
            private static class HuffmanNode implements Comparable<HuffmanNode>{
                // The character of the node
                public char character;
              
                // The frequency of the character
                public int frequency;
              
                // The left node of the root
                public HuffmanNode left;
              
                // The right node of the root
                public HuffmanNode right;
              
                // constructs a new HuffmanNode object with the given character, frequency,
                // and null children
                public HuffmanNode(char character, int frequency) {
                  this(character, frequency, null, null);
                }
              
                // constructs a new HuffmanNode object with the given character, frequency, and children
                public HuffmanNode(char character, int frequency, HuffmanNode left, HuffmanNode right) {
                  this.character = character;
                  this.frequency = frequency;
                  this.left = left;
                  this.right = right;
                }
              
                // post = returns an integer (positive, negative, zero) reflecting whether the
                // frequency of this HuffmanNode object is greater than, less than, or equal to
                // the frequency of the given HuffmanNode object respectively.
                public int compareTo(HuffmanNode other) {
                  return Integer.compare(this.frequency, other.frequency);
                }
            }
          }
        </code>
      </pre>
    </div>
    <script src="prism.js"></script>
  </body>
</html>
