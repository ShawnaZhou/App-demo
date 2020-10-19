<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
					
		},
		onHide: function() {
			console.log('App Hide')
		},
		
	}
</script>

<style lang="scss">
	@import "./static/iconfont.css";
	@import "uview-ui/index.scss";
	@import './common/uni.css';
	/*每个页面公共css */
page{
	font-family: Note Sans Han;
}
</style>
