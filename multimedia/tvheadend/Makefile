# Created by: Bernhard Froehlich <decke@FreeBSD.org>
# $FreeBSD$

PORTNAME=	tvheadend
PORTVERSION=	3.9.20140730
CATEGORIES=	multimedia

MAINTAINER=	decke@FreeBSD.org
COMMENT=	TV streaming server supporting DVB, IPTV and V4L

LICENSE=	GPLv3

USE_GITHUB=	yes
#GH_ACCOUNT=	${PORTNAME}
GH_ACCOUNT=	dreamcat4
GH_PROJECT=	${PORTNAME}
GH_TAGNAME=	${GH_COMMIT}
GH_COMMIT=	c168cb3

BUILD_DEPENDS=	bash:${PORTSDIR}/shells/bash \
		${LOCALBASE}/include/linux/videodev2.h:${PORTSDIR}/multimedia/v4l_compat

LIB_DEPENDS=	libexecinfo.so:${PORTSDIR}/devel/libexecinfo \
		libcurl.so:${PORTSDIR}/ftp/curl

RUN_DEPENDS=	dtv-scan-tables>=0:${PORTSDIR}/multimedia/dtv-scan-tables

USES=		pkgconfig gmake
USE_PYTHON=	yes
GNU_CONFIGURE=	yes
MAKE_JOBS_UNSAFE=	yes
USE_RC_SUBR=	${PORTNAME}

USERS=		tvheadend
GROUPS=		tvheadend webcamd

CONFIGURE_ENV+=	PLATFORM=freebsd
CONFIGURE_ARGS+=--disable-zlib --disable-libav --disable-dvbscan
CFLAGS+=	-I${LOCALBASE}/include -Wno-conversion -Wno-int-to-pointer-cast -Wno-gnu
LDFLAGS+=	-L${LOCALBASE}/lib -lexecinfo -lssl -lz

OPTIONS_DEFINE=	DVBCSA AVAHI XMLTV
OPTIONS_DEFAULT=DVBCSA

DVBCSA_DESC=		Replace internal ffdecsa with dvbcsa
DVBCSA_LIB_DEPENDS=	libdvbcsa.so:${PORTSDIR}/multimedia/libdvbcsa
DVBCSA_CONFIGURE_ENABLE=dvbcsa

AVAHI_LIB_DEPENDS=	libavahi-client.so:${PORTSDIR}/net/avahi-app

XMLTV_RUN_DEPENDS=	p5-xmltv>=0:${PORTSDIR}/textproc/p5-xmltv

.include <bsd.port.pre.mk>

.if ${OSVERSION} < 900000
.if ${ARCH} == "amd64" || ${ARCH} == "sparc64" || ${ARCH} == "mips64"
CFLAGS+=	-D__WORDSIZE=64
.else
CFLAGS+=	-D__WORDSIZE=32
.endif
.endif

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
	# suppress clang errors on FreeBSD 10+
	# @${REINPLACE_CMD} 's|clang|clang-options-disabled|' \

	# because clang is identified as 'cc' on FreeBSD 10+
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
