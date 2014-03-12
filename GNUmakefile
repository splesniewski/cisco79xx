#
#
#
################################################################
# Interesting URLs
# http://tipsandtricks.nogoodatcoding.com/2008/06/hacking-cisco-7940-ip-phone.html#hackciscophone-createringtone
# http://www.loligo.com/asterisk/Cisco/79xx

################################################################
# source files for the conversion
SRCIMAGE=_src/glyph.png
SRCAUDIO=_src/sox.aiff

#----------------------------------------------------------------
# generated files
OUTAUDIO=\
	sox/ringtone.raw

OUTIMAGES=\
	netpbm/glyph.bmp \
	imagemagick/glyph.bmp

################################################################
.PHONY: images audio all clean
all: audio images
images: $(OUTIMAGES)
audio:$(OUTAUDIO) 

clean:
	-rm -f *~
	-rm -f $(OUTAUDIO) $(OUTIMAGES) 
	-rm -rf sox netpbm imagemagick _src

_src/.mkdir:
	-mkdir _src
	date > $@

################################################################
# AUDIO

# Generate a sound to be used as the ringtone.
# (saves me from haveing to find one).
# (based on 'play' example from SoX v14.2.0 manpage)
_src/sox.aiff: _src/.mkdir
	sox -n $@ \
		synth sin %-21.5 sin %-14.5 sin %-9.5 sin %-5.5 \
		sin %-2.5 sin %2.5 gain -5.4 fade h 0.008 2 1.5 \
		delay 0 .27 .54 .76 1.01 1.3 remix - fade h 0.1 2.72 2.5

#----------------------------------------------------------------
# SoX method
sox/.mkdir:
	-mkdir sox
	date > $@

# final ringtone quality isn't very good but what can be expected from 8k/8bit.
sox/ringtone.raw: sox/.mkdir
sox/ringtone.raw: $(SRCAUDIO)
	sox \
		-t aiff $< \
		-t raw -e mu-law -b 8 -c 1 $@ rate -l 8000 trim 0s 16080s
# old syntax: sox -t wav in.wav -t raw -r 8000 -U -b -c 1 out.raw resample -ql

################################################################
# IMAGES

# Generate an image to be used as the splash. 
# (saves me from haveing to find one).
_src/glyph.png:
	echo "=)" \
	| convert -font Courier -background white -pointsize $$((8 * 72)) -trim +repage label:@-  \
	$@
# Would have liked to use this to get a nice smiley but some platforms+fonts are missing the glyph
# 	perl -e 'binmode(STDOUT, ":utf8"); print "\x{263A}";' \

# 2-bit gray palette (phone colors)
_src/palette.pgm:
	(echo P2; echo 4 1; echo 3; echo 0 1 2 3) \
	> $@

#----------------------------------------------------------------
# NetPBM method
netpbm/.mkdir:
	-mkdir netpbm
	date > $@

netpbm/white.ppm: netpbm/.mkdir
	ppmmake rgb:ff/ff/ff 90 56 \
	> $@

netpbm/resize.pgm: netpbm/.mkdir
netpbm/resize.pgm: $(SRCIMAGE)
	pngtopnm $< \
	| pnmscale -ysize 48 \
	| ppmtopgm \
	> $@
#	| ppmbrighten -saturation +25 \

netpbm/glyph.bmp: netpbm/white.ppm netpbm/resize.pgm _src/palette.pgm
	pnmcomp \
		-align=center -valign=middle \
		netpbm/resize.pgm netpbm/white.ppm \
	| ppmtopgm \
	| pnmremap -mapfile=_src/palette.pgm -nofloyd \
	| ppmtobmp -windows -bpp 8 \
	> $@

#----------------------------------------------------------------
# ImageMagicK method
imagemagick/.mkdir:
	-mkdir imagemagick
	date > $@

imagemagick/white.png: imagemagick/.mkdir
imagemagick/white.png:
	convert \
	-size 90x56 \
	xc:white \
	$@

imagemagick/resize.png: imagemagick/.mkdir
imagemagick/resize.png: $(SRCIMAGE)
	convert $< \
	-resize 90x54 \
	$@

imagemagick/composite.png: imagemagick/white.png imagemagick/resize.png
	composite \
	-gravity center imagemagick/resize.png imagemagick/white.png \
	$@

#imagemagick/glyph.bmp: _src/palette.pgm
imagemagick/glyph.bmp: imagemagick/composite.png
	convert $< \
	-compress None \
	-type Grayscale \
	-colors 256 \
	BMP3:$@
	identify $@
