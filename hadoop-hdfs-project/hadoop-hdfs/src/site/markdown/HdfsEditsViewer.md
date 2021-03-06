<!---
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

Offline Edits Viewer Guide
==========================

* [Offline Edits Viewer Guide](#Offline_Edits_Viewer_Guide)
    * [Overview](#Overview)
    * [Usage](#Usage)
    * [Case study: Hadoop cluster recovery](#Case_study:_Hadoop_cluster_recovery)

Overview
--------

Offline Edits Viewer is a tool to parse the Edits log file. The current processors are mostly useful for conversion between different formats, including XML which is human readable and easier to edit than native binary format.

The tool can parse the edits formats -18 (roughly Hadoop 0.19) and later. The tool operates on files only, it does not need Hadoop cluster to be running.

Input formats supported:

1.  **binary**: native binary format that Hadoop uses internally
2.  **xml**: XML format, as produced by xml processor, used if filename
    has `.xml` (case insensitive) extension

The Offline Edits Viewer provides several output processors (unless stated otherwise the output of the processor can be converted back to original edits file):

1.  **binary**: native binary format that Hadoop uses internally
2.  **xml**: XML format
3.  **stats**: prints out statistics, this cannot be converted back to
    Edits file

Usage
-----

       bash$ bin/hdfs oev -i edits -o edits.xml

|                                       Flag | Description |
|:---- |:---- |
| [`-i` ; `--inputFile`] *input file* | Specify the input edits log file to process. Xml (case insensitive) extension means XML format otherwise binary format is assumed. Required. |
| [`-o` ; `--outputFile`] *output file* | Specify the output filename, if the specified output processor generates one. If the specified file already exists, it is silently overwritten. Required. |
| [`-p` ; `--processor`] *processor* | Specify the image processor to apply against the image file. Currently valid options are `binary`, `xml` (default) and `stats`. |
| [`-v` ; `--verbose`] | Print the input and output filenames and pipe output of processor to console as well as specified file. On extremely large files, this may increase processing time by an order of magnitude. |
| [`-h` ; `--help`] | Display the tool usage and help information and exit. |

Case study: Hadoop cluster recovery
-----------------------------------

In case there is some problem with hadoop cluster and the edits file is corrupted it is possible to save at least part of the edits file that is correct. This can be done by converting the binary edits to XML, edit it manually and then convert it back to binary. The most common problem is that the edits file is missing the closing record (record that has opCode -1). This should be recognized by the tool and the XML format should be properly closed.

If there is no closing record in the XML file you can add one after last correct record. Anything after the record with opCode -1 is ignored.

Example of a closing record (with opCode -1):

      <RECORD>
        <OPCODE>-1</OPCODE>
        <DATA>
        </DATA>
      </RECORD>
