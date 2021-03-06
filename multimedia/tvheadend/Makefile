# Created by: Bernhard Froehlich <decke@FreeBSD.org>
# $FreeBSD$

PORTNAME=	tvheadend
PORTVERSION=	3.9.20140806
CATEGORIES=	multimedia

MAINTAINER=	decke@FreeBSD.org
COMMENT=	TV streaming server supporting DVB, IPTV and V4L

LICENSE=	GPLv3

TVH_TESTING=	43253f8
TVH_MASTER=	6d19440

#DC4_LIBAV=	3380ef4
#GH_ACCOUNT=	dreamcat4
#GH_COMMIT=	${DC4_LIBAV}

USE_GITHUB=	yes
GH_ACCOUNT=	${PORTNAME}
GH_PROJECT=	${PORTNAME}
GH_TAGNAME=	${GH_COMMIT}
GH_COMMIT=	${TVH_MASTER}

BUILD_DEPENDS=	bash:${PORTSDIR}/shells/bash \
		${LOCALBASE}/include/linux/videodev2.h:${PORTSDIR}/multimedia/v4l_compat

LIB_DEPENDS=	libexecinfo.so:${PORTSDIR}/devel/libexecinfo \
		libcurl.so:${PORTSDIR}/ftp/curl \
		liburiparser.so:${PORTSDIR}/net/uriparser

RUN_DEPENDS=	dtv-scan-tables>=0:${PORTSDIR}/multimedia/dtv-scan-tables

USES=		pkgconfig gmake
USE_PYTHON=	yes
GNU_CONFIGURE=	yes
MAKE_JOBS_UNSAFE=	yes
USE_RC_SUBR=	${PORTNAME}

USERS=		tvheadend
GROUPS=		tvheadend webcamd

CONFIGURE_ENV+=	PLATFORM=freebsd
CONFIGURE_ARGS+=--disable-dvbscan --disable-zlib
CFLAGS+=	-I${LOCALBASE}/include -Wno-conversion -Wno-int-to-pointer-cast
LDFLAGS+=	-L${LOCALBASE}/lib -lexecinfo -lssl -lz

OPTIONS_DEFINE=	DVBCSA AVAHI FFMPEG XMLTV
OPTIONS_DEFAULT=DVBCSA

DVBCSA_DESC=		Replace internal ffdecsa with dvbcsa
DVBCSA_LIB_DEPENDS=	libdvbcsa.so:${PORTSDIR}/multimedia/libdvbcsa
DVBCSA_CONFIGURE_ENABLE=dvbcsa

AVAHI_LIB_DEPENDS=	libavahi-client.so:${PORTSDIR}/net/avahi-app

FFMPEG_LIB_DEPENDS=	libavcodec.so:${PORTSDIR}/multimedia/ffmpeg
FFMPEG_CONFIGURE_ENABLE=libav

XMLTV_RUN_DEPENDS=	p5-xmltv>=0:${PORTSDIR}/textproc/p5-xmltv

.include <bsd.port.pre.mk>

post-patch:
	@${REINPLACE_CMD} 's|0.0.0~unknown|${PORTVERSION}-${GH_COMMIT}|' \
		${WRKSRC}/support/version
	@${REINPLACE_CMD} 's|-g||' \
		${WRKSRC}/Makefile
	@${REINPLACE_CMD} 's|-ldl||' \
		${WRKSRC}/Makefile
	# ignore gcc warnings on FreeBSD 9.X, and clang warnings on FreeBSD 10+
	@${REINPLACE_CMD} 's|-Werror||' \
		${WRKSRC}/Makefile
.if ${OSVERSION} >= 1000000
	# to suppress clang CC flags on FreeBSD 10+
	# @${REINPLACE_CMD} 's|clang|clang-options-disabled|' \
	# to enable clang CC flags, because clang is identified as 'cc' on FreeBSD 10+
	@${REINPLACE_CMD} 's|clang|cc|' \
		${WRKSRC}/Makefile
.endif
	@${REINPLACE_CMD} 's|#\!/bin/bash|#\!${LOCALBASE}/bin/bash|' \
		${WRKSRC}/Autobuild.sh \
		${WRKSRC}/configure \
		${WRKSRC}/support/changelog \
		${WRKSRC}/support/configure.inc \
		${WRKSRC}/support/getmuxlist \
		${WRKSRC}/support/pbuilder \
		${WRKSRC}/support/version

.include <bsd.port.post.mk>

