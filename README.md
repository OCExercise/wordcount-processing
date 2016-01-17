# wordcount-processing

Experiment in working with mixed Java and Scala, Flink, and Spring (Boot)

## Usage

### SocketTextStreamWordCount

The socket text stream word counter that came with the box. Useful for making sure everything is set up properly

#### Server

```
# You may have to use ncat instead of nc on OS X.  
# The ruby one-liner generates a nice long stream of
# words randomly selected from the first ten in 
# /usr/share/dict/words and prints them to the listener

$ nc -lk -p 9999 --sh-exec "ruby -e \"a=STDIN.readlines;25.times do;b=[];4.times do; b << a[rand(10)].chomp end; puts b.join(' '); end\" < /usr/share/dict/words"
```

#### Gradle
```

# In another shell, run the following

$ gradle bootRun -Pargs="localhost <port of you selected>"

# Observe output 
```

#### Maven

```
# You'll need a bleeding edge build of Flink 

$ git clone 

# In another shell, run the following

$ mvn sprint-boot:run -Drun.arguments="localhost <port of your choice>"

# Observe output 
```

