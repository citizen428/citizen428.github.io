---
layout: post
title: Using Java libraries from Clojure
date: 2009-9-6
comments: true
categories:
- clojure
- java
- programming
---
After reading <a href="http://www.engineyard.com/blog/2009/scripting-java-libraries-with-jruby/">Scripting Java Libraries With Ruby</a> on the <a href="http://www.engineyard.com/">Engine Yard blog</a>, I decided to translate the two example programs to Clojure. Please keep in mind though that I'm pretty much a Clojure newbie myself, so the code shown here might not be very idiomatic. In case you have any suggestions on how to improve the code examples presented here, please leave a comment, thank you! To get the most out of this post, you should probably read the <a href="http://java.ociweb.com/mark/clojure/article.html#JavaInterop">section on Java Interoperability</a> in <a href="http://java.ociweb.com/mark/">Mark Volkmann</a>'s excellent <a href="http://java.ociweb.com/mark/clojure/article.html">Clojure tutorial</a> first.
<p><b>A MIDI Keyboard</b></p>
The first example listens for a line of text which then gets converted into a playable MIDI sequence. Let's start with importing the classes we need.
{% codeblock lang:clojure %}
(ns midi/keyboard
  (:import [javax.sound.midi MidiSystem Sequence MidiEvent ShortMessage])
  (:use [clojure.contrib.seq-utils :only (indexed)]))
{% endcodeblock %}
Next we set up a <a href="http://clojure.org/special_forms#toc9">loop</a> and an exit clause.  In case the user enters the word "exit", we call System.exit to shut down all the threads that got started by Java's MIDI support.
{% codeblock lang:clojure %}
(loop [line (read-line)]
  (if (re-find #"exit" line) (System/exit 0))
  ... other code snipped...
  (recur (read-line)))
{% endcodeblock %}
The main part of the application constructs a new MIDI sequence by creating a NOTE_ON event for every character in the entered text and then finishing the sequence with a STOP event. The original article states that it favors simplicity over efficiency, so the same also holds true for my "translation".
{% codeblock lang:clojure %}
  (def sq (Sequence. Sequence/PPQ 2))
  (def track (.createTrack sq))
  (doseq [[idx note] (indexed line)]
    (let [msg (ShortMessage.)]
      (.setMessage msg ShortMessage/NOTE_ON (int note) 64)
      (.add track (MidiEvent. msg idx))))
  (let [msg (ShortMessage.)]
    (.setMessage msg ShortMessage/STOP)
    (.add track (MidiEvent. msg (+ (.length line) 1))))
{% endcodeblock %}
To finish off our first program we play the sequence before prompting for more input (see the loop snippet above).
{% codeblock lang:clojure %}
  (doto (MidiSystem/getSequencer)
    (.open)
    (.setSequence sq)
    (.start))
{% endcodeblock %}
<a href="http://github.com/citizen428/codequickies/blob/master/clojure/midi/keyboard.clj">Full source for the first example</a>
<p><b>Making It More Interactive</b></p>
While the first example was fun, it wasn't as interactive as it could be. Therefore the second program launches a GUI window to receive keystroke events, turning a note on or off when a key is pressed or released. Since we don't have to create MIDI sequences this time, our import statement becomes a lot shorter:
{% codeblock lang:clojure %}
(ns midi/interactive
  (:import [javax.sound.midi MidiSystem]
           [javax.swing JFrame] [java.awt.event KeyListener]))
{% endcodeblock %}
This example directly accesses the system synthesizer. The following lines open the default synthesizer's channel 0 (and also create a JFrame object which we'll use later):
{% codeblock lang:clojure %}
(let [synth (MidiSystem/getSynthesizer)
      frame (JFrame. "Music Frame")]
  (.open synth)
  (let [channel (aget (.getChannels synth) 0)]
{% endcodeblock %}
We then launch a GUI frame as a simple and cross-platform way to receive keystroke events. The KeyListener interface is implemented using Clojure's <a href="http://clojure.org/java_interop#toc26">proxy macro</a>, using keyPressed and keyReleased events to turn notes on or off.
{% codeblock lang:clojure %}
(doto frame
      (.setSize 300 300)
      (.setDefaultCloseOperation JFrame/EXIT_ON_CLOSE)
      (.addKeyListener
       (proxy [KeyListener] []
         (keyPressed [e]
                     (.noteOn channel (int (.getKeyChar e)) 64))
         (keyReleased [e]
                      (.noteOff channel (int (.getKeyChar e))))
         (keyTyped [e])))
{% endcodeblock %}
Last but not least we display our frame to let the fun begin.
{% codeblock lang:clojure %}
      (.setVisible true))))
{% endcodeblock %}
<a href="http://github.com/citizen428/codequickies/blob/master/clojure/midi/interactive.clj">Full source for the second example</a><br>
I hope this article helped to illustrate how easy it is to use the plethora of existing Java libraries from Clojure. In case you have any feedback or questions, please feel free to leave a comment!
