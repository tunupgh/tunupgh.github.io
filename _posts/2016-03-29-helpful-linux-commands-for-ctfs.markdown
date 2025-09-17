---
layout: post
title: Helpful Linux Commands for CTFs
date: 2016-03-29 23:13
comments: true
categories:
- linux
- misc
---
When playing certain CTF games there are often challenges that require you to do crypto analysis, packet analysis, deciphering text, or file manipulation. There are some really powerful linux command line tools that can help solve these problems. Below are some notes of commands I've used to solve challenges.



### file
This command is common on most linux systems. It will tell you what type of file you have. Consider the following usage examples:
<pre>
file selfie.png
  selfie.png: PNG image data, 256 x 192, 8-bit/color RGBA, non-interlaced

file 12a18338
  12a18338: tcpdump capture file (little-endian) - version 2.4 (raw IP, capture length 1600)
</pre>
<br><br>


### base64
This usually comes installed in linux. You can use it to decode base64 text. If you have a base64 string called `Z3JlYXQgam9i` you could decode it this way:
<pre>
echo 'Z3JlYXQgam9i'>2.txt
base64 -d 2.txt
</pre>
Essentially we are simply adding the string `Z3JlYXQgam9i` into the file 2.txt. Then we are decoding (-d) the file with the base64 command. This can also be done in one line by doing `echo 'Z3JlYXQgam9i' | base64 -d`
<br><br>
Pro Tip! The base64 character 'A' represents a null or 0 value. If you need to pad a malformed base64 string with extra characters, 'A' is probably the best choice.


### hd
This is the hex dump command. It's common in many linux distros but may need to be installed. If you still have the 2.txt file from the example above, try the following command:
<pre>
base64 -d 2.txt | hd
</pre>
The result is a display of each hex value of each ascii character in the file. Take note of the | command. The hd command will be executed on the result of what was displayed to the left of the | command. You are piping the output to another command.
<br><br>

Pro Tip! If you're looking at a hex dump and one line simply displays a `*` and no other data, it means the line above repeated X many times. To find how many times it repeated, count between the line numbers of the hex dump before and after the *.


### xor
This program can be downloaded from <a href="http://dirtbags.net/tools/">dirtbags.net</a> under the tools section. First download xor.c then compile it using the following command `gcc xor.c -o xor`. This will create an executable program from the code.
XOR is used extensively in many encryption algorithms. One reason it is popular is because it is easy to implement in hardware, since there is no possibility for overflow or underflow, there are no "carry" bits as in addition, and XOR is one of the basic logic gates used in electronics. For these reasons, it is also one of the quickest operations most CPUs can carry out.
This xor program syntax is `./xor <key> <text>`. Where the key is the value to manipulate the bits with and the text is either the text or ciphertext that will be xor'd.

Consider trying the following example commands:
<pre>
echo "hello" | ./xor 22
</pre>
This should display something like `~szzy`
Now try the reverse.
<pre>
echo "~szzy" | ./xor 22
</pre>

Most of the time I have a bunch of data in a file and use the xor command on the file. In that case you would do something like this:
<pre>
./xor 0x22 < filename.txt
</pre>

The xor command expects standard input. So that's why you have to either pipe it or feed in a file using the `<` command.

Also take note on the xor program that there is a big difference between “./xor 22 < file.txt” and “./xor 0x22 < file.txt”. The second one is a hex number while the first is decimal. You can indicate your value is hex by using the –x command like this:
<pre>
./xor -x 22 < file.txt
</pre>

You can also have a multi value key which would look like this:
<pre>
./xor -x 01 a0 22 f5 < file.txt
</pre>

<br><br>

### unhex
The file unhex.c is also downloaded at <a href="http://dirtbags.net/tools/">dirtbags.net</a> under tools. After downloading the file compile it using `gcc unhex.c -o unhex`. This tool essentially converts hex to ascii. It may actually be better named as hextoascii. Try the following commands:
<pre>
echo "67 72 65 61 74 20 6a 6f  62" > hex.txt
./unhex < hex.txt
</pre>
We put the data “67 72 65 61 74 20 6a 6f  62” into the file hex.txt. Then we use the hex tool to convert those numbers from hex to ASCII which results in "great job".

<br><br>
Super pro tip! A hex value of 0x00 is an ASCII "null" character. These puzzles often us null characters in place of spaces. A space is 0x20 in hex. Therefore if you are seeing a bunch of null characters you may want to try to xor it with 0x20 to change nulls to spaces. ALSO as an added bonus, the 0x20 is also the value difference between lowercase and uppercase ASCII. So you'll convert all upper to lower and lower to upper when you do that.
<br><br>

### tr
The command `tr` usually comes standard in most linux distros. It's a command that you can use to replace text. Here are some examples:

* Change all lowercase to upper case *
`tr a-z A-Z`

* Conduct a ROT13 *
`tr '[A-Za-z]' '[N-ZA-Mn-za-m]'`

* Remove all carriage returns *
`tr -d '\r' < inputfile.txt > outputfile.txt`
The `-d` is to delete.

* Remove all whitespace in a file *
`tr -d ' \t\n\r\f' <inputFile >outputFile`

### cut
This command lets you remove text on each line.

`cat file.txt | cut -d ' ' -f 2-`

Removes the first word of every line. Useful for removing line numbers within text documents.

<br><br>

### sort and uniq
The `sort` and `uniq` commands usually come with most linux installs. Sort will sort your input line by line alphanumerically. Uniq will deduplicate data and show you only unique rows of the data. In combination these two commands are powerful. That is because Uniq won't detect repeated lines unless they are adjacent. So sorting the input first helps uniq do it's thing.

`cat file.txt | sort | uniq`

This will sort the file contents so that each row is now sorted alphabetically. Then uniq will deduplicate rows. Resulting in all of the unique rows of that file.

`cat file.hd | xargs -n1 | sort | uniq -c`

In this case we have a hexdump and are looking for the most common word in the file. Command `xargs -n1` will extra each word into its own row. Then sort will sort all rows alphabetically, the goal here is to put all duplicate words together. Then `uniq -c` will count the number of times each row occurs.


### strings
This command comes with most linux installs too. It will examine a file and spit out any strings or words it finds in it. This is handy for looking for hidden strings in imgs or pdf files.

`strings file.pdf`

### dd
If you want to cut off bytes from the beginning of each line, you can use `dd` to accomplish this.

`dd bs=1 skip=7`

This command will set your block size to 1 byte, and then skip the first 7 bytes of that line.

### exiftool
The exiftool is a linux command line program which extracts exif data from images.

<br><br>
<h4>tcpflow</h4>
Can easily be found and installed in linux. Will extract tcp flows from a pcap (wireshark) file and put the results into files in that directory. This is equivalent to opening wireshark, doing follow tcp stream, and saving all the streams to files. Sometimes there are bugs in wireshark which doesn't handle pcaps very well to dump flows properly. Using tcpflow to extract flows can help you see more of the raw data in packets. To extract the flows use it like this:
<pre>tcpflow -r standard.pcap</pre>

<br><br>

<h4>tcpdump</h4>
This program is usually used to capture packets on a linux host, however it can also be used to display the contents of a wireshark pcap file. To display the data you can do a command like this:
<pre>tcpdump -nXr 12a18338.pcap</pre>

This displays the contents of the file 12a18338.pcap. You can also add a -x to the end to display the hex values of each packet.




### One liner chaining examples
Here are some examples of chaining the above commands to conduct some powerful data manipulation.


`tr -d '\r' < ciphertext.b64 | base64 -d > answer.jpg`
The file we had was a base 64 encoded image. It had carriage returns in it. This command removed them, decoded the base 64 text, and placed the result in a jpg file to view the data properly.


`for i in $(seq 1 255); do cat file.hd | ./unhex | ./xor $i | hd; done | less`
In this case we think the file.hd hexdump is xor'd but we don't know the mask. So we are going to apply all masks from 1 to 255 and then walk through the output to try to see if any of those is the right xor mask. Sort of brute forcing the xor encryption to try to find which one is the xor byte mask.


`while read l; do echo $l; done`
This command will read each line and put it into variable $l. This is useful for times when you need to do an action on each individual line instead of the whole file. Such as when each line is it's own base64 encoded line instead of the whole file being a base64 decoded file.


`tcpdump -nxr file.pcap src host 10.1.2.12 | awk '/IP/ {print "";} /0x/ printf("%s", $2 $3 $4 $5 $6 $7 $8 $9);}' | while read l; do echo $l | ./unhex | dd bs=1 skip=34 2>/dev/null; done | less`
This command will look at the payload data of a pcap file with a source IP of 10.1.2.12. It then removes the header information and prints the data from each packet on its own line. It then reads each line and converts it to ascii while removing the first 34 bytes which is some kind of header information we didn't need.


`cat hint.txt | sed 's/\s/\n/g' | awk '!(NR%3)' | tr -d ' \t\n\r\f' | ./unhex `
The input file is a hex dump. Put every word on a new line, print every 3rd word, remove all whitespace, convert hex to ascii. This essentially keeps every 3rd hex char and drops everything else.