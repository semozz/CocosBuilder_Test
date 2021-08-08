/*
 * cocos2d for iPhone: http://www.cocos2d-iphone.org
 *
 * Copyright (c) 2008-2010 Ricardo Quesada
 * Copyright (c) 2011 Zynga Inc.
 *
 * Permission is hereby granted, free of charge, to any person obtaining a copy
 * of this software and associated documentation files (the "Software"), to deal
 * in the Software without restriction, including without limitation the rights
 * to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 * copies of the Software, and to permit persons to whom the Software is
 * furnished to do so, subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be included in
 * all copies or substantial portions of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
 * THE SOFTWARE.
 *
 */


#import "CCLabelTTF.h"
#import "Support/CGPointExtension.h"
#import "ccMacros.h"
#import "CCShaderCache.h"
#import "CCGLProgram.h"
#import "Support/CCFileUtils.h"
#import "ccDeprecated.h"

#ifdef __CC_PLATFORM_IOS
#import "Platforms/iOS/CCDirectorIOS.h"
#import <CoreText/CoreText.h>
#endif

#if CC_USE_LA88_LABELS
#define SHADER_PROGRAM kCCShader_PositionTextureColor
#else
#define SHADER_PROGRAM kCCShader_PositionTextureA8Color
#endif

@interface CCLabelTTF ()
- (BOOL) updateTexture;
- (NSString*) getFontName:(NSString*)fontName;
@end

@implementation CCLabelTTF

// -
+ (id) labelWithString:(NSString*)string fontName:(NSString*)name fontSize:(CGFloat)size
{
	return [[[self alloc] initWithString:string fontName:name fontSize:size]autorelease];
}

// hAlignment
+ (id) labelWithString:(NSString*)string fontName:(NSString*)name fontSize:(CGFloat)size dimensions:(CGSize)dimensions hAlignment:(CCTextAlignment)alignment
{
	return [[[self alloc] initWithString:string  fontName:name fontSize:size dimensions:dimensions hAlignment:alignment vAlignment:kCCVerticalTextAlignmentTop lineBreakMode:kCCLineBreakModeWordWrap]autorelease];
}

// hAlignment, vAlignment
+ (id) labelWithString:(NSString*)string fontName:(NSString*)name fontSize:(CGFloat)size dimensions:(CGSize)dimensions hAlignment:(CCTextAlignment)alignment vAlignment:(CCVerticalTextAlignment) vertAlignment
{
	return [[[self alloc] initWithString:string fontName:name fontSize:size dimensions:dimensions hAlignment:alignment vAlignment:vertAlignment]autorelease];
}

// hAlignment, lineBreakMode
+ (id) labelWithString:(NSString*)string fontName:(NSString*)name fontSize:(CGFloat)size dimensions:(CGSize)dimensions hAlignment:(CCTextAlignment)alignment lineBreakMode:(CCLineBreakMode)lineBreakMode
{
	return [[[self alloc] initWithString:string fontName:name fontSize:size dimensions:dimensions hAlignment:alignment vAlignment:kCCVerticalTextAlignmentTop lineBreakMode:lineBreakMode]autorelease];
}

// hAlignment, vAlignment, lineBreakMode
+ (id) labelWithString:(NSString*)string fontName:(NSString*)name fontSize:(CGFloat)size dimensions:(CGSize)dimensions hAlignment:(CCTextAlignment)alignment vAlignment:(CCVerticalTextAlignment) vertAlignment lineBreakMode:(CCLineBreakMode)lineBreakMode
{
	return [[[self alloc] initWithString:string fontName:name fontSize:size dimensions:dimensions hAlignment:alignment vAlignment:vertAlignment lineBreakMode:lineBreakMode]autorelease];
}

- (id) init
{
    return [self initWithString:@"" fontName:@"Helvetica" fontSize:12];
}

- (id) initWithString:(NSString*)str fontName:(NSString*)name fontSize:(CGFloat)size
{
	return [self initWithString:str fontName:name fontSize:size dimensions:CGSizeZero hAlignment:kCCTextAlignmentLeft vAlignment:kCCVerticalTextAlignmentTop lineBreakMode:kCCLineBreakModeWordWrap];
}

// hAlignment
- (id) initWithString:(NSString*)str fontName:(NSString*)name fontSize:(CGFloat)size dimensions:(CGSize)dimensions hAlignment:(CCTextAlignment)alignment
{
	return [self initWithString:str fontName:name fontSize:size dimensions:dimensions hAlignment:alignment vAlignment:kCCVerticalTextAlignmentTop lineBreakMode:kCCLineBreakModeWordWrap];
}

// hAlignment, vAlignment
- (id) initWithString:(NSString*)str fontName:(NSString*)name fontSize:(CGFloat)size dimensions:(CGSize)dimensions hAlignment:(CCTextAlignment)alignment vAlignment:(CCVerticalTextAlignment) vertAlignment
{
	return [self initWithString:str fontName:name fontSize:size dimensions:dimensions hAlignment:alignment vAlignment:vertAlignment lineBreakMode:kCCLineBreakModeWordWrap];
}

// hAlignment, lineBreakMode
- (id) initWithString:(NSString*)str fontName:(NSString*)name fontSize:(CGFloat)size dimensions:(CGSize)dimensions hAlignment:(CCTextAlignment)alignment lineBreakMode:(CCLineBreakMode)lineBreakMode
{
	return [self initWithString:str fontName:name fontSize:size dimensions:dimensions hAlignment:alignment vAlignment:kCCVerticalTextAlignmentTop lineBreakMode:lineBreakMode];
}

// hAlignment, vAligment, lineBreakMode
- (id) initWithString:(NSString*)str  fontName:(NSString*)name fontSize:(CGFloat)size dimensions:(CGSize)dimensions hAlignment:(CCTextAlignment)alignment vAlignment:(CCVerticalTextAlignment) vertAlignment lineBreakMode:(CCLineBreakMode)lineBreakMode
{
	if( (self=[super init]) ) {

		// shader program
		self.shaderProgram = [[CCShaderCache sharedShaderCache] programForKey:SHADER_PROGRAM];

		_dimensions = dimensions;
		_hAlignment = alignment;
		_vAlignment = vertAlignment;
		_fontName = [[self getFontName: name] copy];
		_fontSize = size;
		_lineBreakMode = lineBreakMode;

		[self setString:str];
	}
	return self;
}

- (void) setString:(NSString*)str
{
	NSAssert( str, @"Invalid string" );

	if( _string.hash != str.hash ) {
		[_string release];
		_string = [str copy];
		
		[self updateTexture];
	}
}

-(NSString*) string
{
	return _string;
}

- (NSString*) getFontName:(NSString*)fontName
{
	// Custom .ttf file ?
    if ([[fontName lowercaseString] hasSuffix:@".ttf"])
    {
        // This is a file, register font with font manager
        NSString* fontFile = [[CCFileUtils sharedFileUtils] fullPathForFilename:fontName];
        NSURL* fontURL = [NSURL fileURLWithPath:fontFile];
        CTFontManagerRegisterFontsForURL((CFURLRef)fontURL, kCTFontManagerScopeProcess, NULL);

		return [[fontFile lastPathComponent] stringByDeletingPathExtension];
    }

    return fontName;
}

- (void)setFontName:(NSString*)fontName
{
    fontName = [self getFontName:fontName];
    
	if( fontName.hash != _fontName.hash ) {
		[_fontName release];
		_fontName = [fontName copy];
		
		// Force update
		if( _string )
			[self updateTexture];
	}
}

- (NSString*)fontName
{
    return _fontName;
}

- (void) setFontSize:(float)fontSize
{
	if( fontSize != _fontSize ) {
		_fontSize = fontSize;
		
		// Force update
		if( _string )
			[self updateTexture];
	}
}

- (float) fontSize
{
    return _fontSize;
}

-(void) setDimensions:(CGSize) dim
{
    if( dim.width != _dimensions.width || dim.height != _dimensions.height)
	{
        _dimensions = dim;
        
		// Force update
		if( _string )
			[self updateTexture];
    }
}

-(CGSize) dimensions
{
    return _dimensions;
}

-(void) setHorizontalAlignment:(CCTextAlignment)alignment
{
    if (alignment != _hAlignment)
    {
        _hAlignment = alignment;
        
        // Force update
		if( _string )
			[self updateTexture];

    }
}

- (CCTextAlignment) horizontalAlignment
{
    return _hAlignment;
}

-(void) setVerticalAlignment:(CCVerticalTextAlignment)verticalAlignment
{
    if (_vAlignment != verticalAlignment)
    {
        _vAlignment = verticalAlignment;
        
		// Force update
		if( _string )
			[self updateTexture];
    }
}

- (CCVerticalTextAlignment) verticalAlignment
{
    return _vAlignment;
}

- (void) setStroke:(CGFloat)stroke
{
	if( stroke != _stroke ) {
		_stroke = stroke;
		
		// Force update
		if( _string )
			[self updateTexture];
	}
}

- (CGFloat) stroke
{
    return _stroke;
}

- (void) setStrokeColor:(ccColor3B)color
{
	_strokeColor = color;
	if (_string)
		[self updateTexture];
}

-(ccColor3B) strokeColor
{
	return _strokeColor;
}

- (void) setShadowPosition:(CGPoint)shadowPosition
{
	if (shadowPosition.x != _shadowPosition.x
		|| shadowPosition.y != _shadowPosition.y)
	{
		_shadowPosition = shadowPosition;
		if (_string)
			[self updateTexture];
	}
}

- (CGPoint) shadowPosition
{
	return _shadowPosition;
}

-(void) setShadowOpacity:(CGFloat)shadowOpacity
{
	_shadowOpacity = shadowOpacity;
	if (_string)
		[self updateTexture];
}

-(CGFloat) shadowOpacity
{
	return _shadowOpacity;
}

- (void) setShadowColor:(ccColor3B)color
{
	_shadowColor = color;
	if (_string)
		[self updateTexture];
}

-(ccColor3B) shadowColor
{
	return _shadowColor;
}

- (void) dealloc
{
	[_string release];
	[_fontName release];

	[super dealloc];
}

- (NSString*) description
{
	// XXX: _string, _fontName can't be displayed here, since they might be already released

	return [NSString stringWithFormat:@"<%@ = %p | FontSize = %.1f>", [self class], self, _fontSize];
}

// Helper
- (BOOL) updateTexture
{
	CCTexture2D *tex;
	if( _dimensions.width == 0 || _dimensions.height == 0 )
		tex = [[CCTexture2D alloc] initWithString:_string
										 fontName:_fontName
										 fontSize:_fontSize  * CC_CONTENT_SCALE_FACTOR()];
	else
		tex = [[CCTexture2D alloc] initWithString:_string
										 fontName:_fontName
										 fontSize:_fontSize  * CC_CONTENT_SCALE_FACTOR()
									   dimensions:CC_SIZE_POINTS_TO_PIXELS(_dimensions)
									   hAlignment:_hAlignment
									   vAlignment:_vAlignment
									lineBreakMode:_lineBreakMode
			   ];

	if( !tex )
		return NO;
	
#ifdef __CC_PLATFORM_IOS
	// iPad ?
	if( UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad ) {
		if( CC_CONTENT_SCALE_FACTOR() == 2 )
			[tex setResolutionType:kCCResolutioniPadRetinaDisplay];
		else
			[tex setResolutionType:kCCResolutioniPad];
	}
	// iPhone ?
	else
	{
		if( CC_CONTENT_SCALE_FACTOR() == 2 )
			[tex setResolutionType:kCCResolutioniPhoneRetinaDisplay];
		else
			[tex setResolutionType:kCCResolutioniPhone];
	}
#endif
	
	[self setTexture:tex];
	[tex release];
	
	CGRect rect = CGRectZero;
	rect.size = [_texture contentSize];
	[self setTextureRect: rect];

	[self removeAllChildren];

	if (_shadowPosition.x != 0 || _shadowPosition.y != 0 || _stroke > 0.f)
	{
		CGSize buffSize = _texture.contentSize;
		CGSize offset = CGSizeMake(_stroke*2+fabs(_shadowPosition.x)*2, _stroke*2+fabs(_shadowPosition.y)*2);
		buffSize.width += offset.width;
		buffSize.height += offset.height;
		
		// back up
		CGPoint orgPos = [self position];
		ccColor3B orgColor = [self color];
		[self setColor:ccBLACK];
		GLubyte orgOpacity = [self opacity];
		[self setOpacity:_shadowOpacity];
		ccBlendFunc orgBlend = [self blendFunc];
		[self setBlendFunc:(ccBlendFunc){GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA}];
		CGPoint bottomLeft = ccp(_texture.contentSize.width * self.anchorPoint.x+offset.width*0.5, self.texture.contentSize.height * self.anchorPoint.y+offset.height*0.5);

		CCRenderTexture* bgSd = [CCRenderTexture renderTextureWithWidth:buffSize.width height:buffSize.height];

		[bgSd beginWithClear:0 g:0 b:0 a:0];
		
		// start render
		// shadow
		if (_shadowPosition.x != 0 || _shadowPosition.y != 0)
		{
			[self setColor:_shadowColor];
			[self setOpacity:_shadowOpacity];
			[self setPosition:ccp(bottomLeft.x+_shadowPosition.x+(_shadowPosition.x<0?-_stroke:_stroke)
								  , bottomLeft.y+_shadowPosition.y+(_shadowPosition.y<0?-_stroke:_stroke))];
			[self visit];
		}
		
		// stroke
		if (_stroke > 0.f)
		{
			[self setColor:_strokeColor];
			[self setOpacity:255];
			
			for (int i=0; i<360; i+=45)
			{
				[self setPosition:ccp(bottomLeft.x + sin(CC_DEGREES_TO_RADIANS(i))*_stroke, bottomLeft.y + cos(CC_DEGREES_TO_RADIANS(i))*_stroke)];
				[self visit];
			}
		}

		[bgSd end];
		// render ended.
		
		// restore
		[self setPosition:orgPos];
		[self setColor:orgColor];
		[self setBlendFunc:orgBlend];
		[self setOpacity:orgOpacity];

		[bgSd setPosition:ccp(_texture.contentSize.width*0.5, _texture.contentSize.height*0.5)];
		[self addChild:bgSd z:-2 tag:1];
	}

	return YES;
}
@end
