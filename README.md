# ggg
#
# SierpinskisCarpenter.py
#
# Author: Dibson T Hoffweiler 
# WWW:    http://www.dibson.net/
#
#             DO WHAT THE FUCK YOU WANT TO PUBLIC LICENSE
#                     Version 2, December 2004
# 
#  Everyone is permitted to copy and distribute verbatim or modified
#  copies of this license document, and changing it is allowed as long
#  as the name is changed.
# 
#             DO WHAT THE FUCK YOU WANT TO PUBLIC LICENSE
#    TERMS AND CONDITIONS FOR COPYING, DISTRIBUTION AND MODIFICATION
# 
#   0. You just DO WHAT THE FUCK YOU WANT TO.
#

import Image

class SierpinskisCarpenter:
    
    def __init__(self):
        """ Prepare the Carpenter """
        self.color = (0, 0, 255)

    def getCounter(self):
        """ Return current counter value, increment it. """
        c = self.counter
        self.counter += 1
        return c

    def getFrameFilename(self):
        """ Return filename for next frame. """
        return "carpet-frame-%02d-%06d.gif" % (self.itr_start, self.getCounter())

    def getCarpetImage(self, itr, size, init=True):
        """ Return an image object of Sierpinski's Carpet

            itr - int - how many iterations deep to go
            size - int - width of carpet in pixels
            init - bool - if True, clear counters - must set False when recursing! (default True)
        """

        if (init):
            # Prepare to lay the carpet out
            self.counter = 0
        
        if (0 == itr):
            # No need to iterate, return the part
            part = Image.new("RGB", (size, size), self.color)
            return part
        else:
            # Get smaller carpet, and paste it in 8 times

            third = size / 3

            partSmall = self.getCarpetImage(itr - 1, third, False)
            
            im = Image.new("RGB", (size, size), (255, 255, 255))

            # top left
            tl_start = (0, 0)
            im.paste(partSmall, tl_start)

            # top center
            tc_start = (third, 0)
            im.paste(partSmall, tc_start)

            # top right
            tr_start = (2 * third, 0)
            im.paste(partSmall, tr_start)

            # middle right
            mr_start = (2 * third, third)
            im.paste(partSmall, mr_start)

            # bottom right
            br_start = (2 * third, 2 * third)
            im.paste(partSmall, br_start)

            # bottom center
            bc_start = (third, 2 * third)
            im.paste(partSmall, bc_start)

            # bottom left
            bl_start = (0, 2 * third)
            im.paste(partSmall, bl_start)

            # middle left
            ml_start = (0, third)
            im.paste(partSmall, ml_start)

            return im

    def animateCarpet(self, itr, size, start=None):
        """ A recursive function to generate an animation of drawing Sierpinski's Carpet.
        
        - itr - int - how many iterations to do
        - size - int - image size will be a square with lengths this many pixels
        - start - 2dim tuple - top left corner of carpet to draw

        Example:

          sc = SierpinskisCarpenter()
          itr = 3
          size = (3 ** itr, 3 ** itr)
          im = sc.animateCarpet(itr, size)
          im.show()

        """

        if (None == start):
            # Init the animation
            start = (0,0)
            self.counter = 0
            self.im = Image.new("RGB", (size, size), (255, 255, 255))
            self.itr_start = itr
            self.im.save(self.getFrameFilename())

        # tab for clean debug output
        tab = "\t" * itr

        #print tab + "MAKE CARPET ITR %s" % (itr)
        #print "%sStart %s" % (tab, start)

        if (0 == itr):
            # LAST ITERATION - PAINT IT!
            #print "%sPAINTING: Start %s" % (tab, start)
            self.im.paste(self.color, (start[0], start[1], start[0] + size, start[1] + size))
            self.im.save(self.getFrameFilename())
        else:
            # NEED TO ITERATE, DO ALL 8 SQUARES

            third = size / 3
    
            # top left
            tl_start = (start[0], start[1])
            self.im = self.animateCarpet(itr-1, size/3, tl_start)
    
            # top center
            tc_start = (start[0] + third, start[1])
            self.im = self.animateCarpet(itr-1, size/3, tc_start)
    
            # top right
            tr_start = (start[0] + 2 * third, start[1])
            self.im = self.animateCarpet(itr-1, size/3, tr_start)
    
            # middle right
            mr_start = (start[0] + 2 * third, start[1] + third)
            self.im = self.animateCarpet(itr-1, size/3, mr_start)

            # bottom right
            br_start = (start[0] + 2 * third, start[1] + 2 * third)
            self.im = self.animateCarpet(itr-1, size/3, br_start)

            # bottom center
            bc_start = (start[0] + third, start[1] + 2 * third)
            self.im = self.animateCarpet(itr-1, size/3, bc_start)
    
            # bottom left
            bl_start = (start[0], start[1] + 2 * third)
            self.im = self.animateCarpet(itr-1, size/3, bl_start)
    
            # middle left
            ml_start = (start[0], start[1] + third)
            self.im = self.animateCarpet(itr-1, size/3, ml_start)
    
        return self.im

cm = SierpinskisCarpenter()

# EXAMPLE: saves 6 iterations of Sierpinski's Carpet
#topItr = 6
#size = 3 ** topItr
#for itr in range(topItr+1):
#    im = cm.getCarpetImage(itr, size)
#    im.save("carpet-%spx-%s.gif" % (size, itr))

# EXAMPLE: dumps out frames for drawing out carpet
#cm.animateCarpet(3, 27)
