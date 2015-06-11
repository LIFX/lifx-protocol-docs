>  [LIFX Developer Home](http://developer.lifx.com) |  [LAN Protocol](README.md). LiFi Labs, Inc. © 2015. All rights reserved. Usage of this documentation is bound by the [LIFX Developer  Terms] (http://developer.lifx.com/terms.html).

# LIFX Protocol V2 documentation tools

To generate the Header description images use
[dformat](https://github.com/sathlan/dformat)

      Install dformat ...
        git clone https://github.com/sathlan/dformat.git
        cd dformat
        ./autogen.sh
        ./configure
        make install

      cd lifx-protocol-docs/header
      for file in *.dformat; do
        dformat.awk <$file | groff -ms -p | convert -scale 200% - `basename $file .dformat`.png
      done

Or, instead of _convert_ use _inkscape_ (for better resolution)

To generate the Workflows diagrams use
[plantuml](http://plantuml.sourceforge.net/)

      Download plantuml.jar
      cd lifx-protocol-docs/workflows
      java -jar plantuml.jar  # automatically processes all .uml files into .png
