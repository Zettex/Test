$(function () {

	let dots = null;
	// Если breadcrumb не пустой
	if ($('.breadcrumb li').length) {

		// Добавляет точки
		dots = $('<li style="display: none; color: #cecde4;">' +
					'<div style="display: inline-block">...</div>' +
				'</li>').insertAfter($('.breadcrumb :first'));
	}

	const childCount = $('.breadcrumb li').length;
	const idxStartHiding = 3;
	const maxTruncateLevel = 200;
	const minTruncateLevel = 0;
	const truncateWidthSize = 30;

	const navbarText = $('.navbar-text');

	let truncateLevel = 0;
	let idxNextHideChild = childCount - (childCount - idxStartHiding);
	let currentThreshold = {
		id: 0,
		thresholds: [
			300,
			120
		],
		get current() {
			return this.thresholds[this.id];
		}
	};

	function resize() {

		const headerWidth = getElementWidth('.navbar.navbar-static-top');
		const idxLastHiddenChild = idxNextHideChild - 1;
		const lastHiddenChildWidth = getElementWidth('.breadcrumb :nth-child(' + idxLastHiddenChild + ')');

		// Сброс к нулевому порогу, если много свободного места в header
		if (getWidthHeaderContent() < headerWidth && currentThreshold.id !== 0) {
			$('.breadcrumb').show();
			currentThreshold.id = 0;
		}

		// Уменьшает ширину текста названия организации
		while (getWidthHeaderContent() > headerWidth &&
			truncateLevel < maxTruncateLevel &&
			getElementWidth(navbarText) - truncateWidthSize >= currentThreshold.current
		) {
			truncateOrganizationName(-truncateWidthSize);
			truncateLevel++;
		}

		// Показывает li, если ширина содержимого header меньше, чем его собственная
		while (getWidthHeaderContent(idxLastHiddenChild === 3) + lastHiddenChildWidth < headerWidth &&
			idxNextHideChild > idxStartHiding
		) {
			$('.breadcrumb :nth-child(' + --idxNextHideChild + ')').show();

			if (idxNextHideChild === idxStartHiding) {
				$(dots).hide();
			}
		}

		// Скрывает li, если ширина содержимого header больше, чем его собственная
		while (getWidthHeaderContent() > headerWidth && idxNextHideChild < childCount) {
			$('.breadcrumb :nth-child(' + idxNextHideChild++ + ')').hide();
			$(dots).show();
		}

		// Восстанавливает ширину названия организации, если есть свободное место
		while (getWidthHeaderContent() + truncateWidthSize < headerWidth &&
			truncateLevel > minTruncateLevel && 
			idxNextHideChild === idxStartHiding
		) {
			const fullSizeNavbarText = document.querySelector(navbarText.selector).scrollWidth;
			const organizationNameWidth = getElementWidth(navbarText);
			let setWidthAuto = false;
			// Если вся ширина наз. орг. восстановлена
			if (fullSizeNavbarText <= organizationNameWidth + truncateWidthSize) {
				setWidthAuto = true;
			}

			truncateOrganizationName(truncateWidthSize, setWidthAuto);
			truncateLevel--;
		}

		// Если все li breadcrumb скрыты и ширина содержимого header меньше, чем его собственная
		if ((idxNextHideChild === childCount || !childCount) && getWidthHeaderContent() > headerWidth) {

			currentThreshold.id = 1;
			// Если перешел за первый порог, а ширина все равно больше
			while (getWidthHeaderContent() > headerWidth &&
				truncateLevel < maxTruncateLevel &&
				getElementWidth(navbarText) - truncateWidthSize >= currentThreshold.current
			) {
				truncateOrganizationName(-truncateWidthSize);
				truncateLevel++;
			}

			// Если все li скрыты и ширина имени организации зашла за порог 1, 
			// а места все равно не хватает, то скрывается breadcrumb
			if (getWidthHeaderContent() > headerWidth) {
				$('.breadcrumb').hide();
			}
		}

	}

	function truncateOrganizationName(width, setWidthAuto) {
		setWidthAuto = setWidthAuto || false;

		if (setWidthAuto) {
			$(navbarText).css('width', 'auto');
			return;
		}

		$(navbarText).width($(navbarText).width() + width);
	}

	function getWidthHeaderContent(isLastShowLi) {
		const marginOffset = 10;
		return getElementWidth('.breadcrumb') +
			getElementWidth('.navbar-custom-menu') +
			getElementWidth('.sidebar-toggle') +
			marginOffset +
			(isLastShowLi ? -getElementWidth(dots) : 0);
	}

	function getElementWidth(name) {
		return $(name).outerWidth();
	}

	$(window).resize(resize);
	resize();
});
