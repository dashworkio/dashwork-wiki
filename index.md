### Welcome to Dashwork Developer Docs

You can use these docs for getting a quick psuedo overview of the workflow behind. These workflows include Theme & Plugin development automations etc.


## Gulp Based Theme Development Workflow

Our gulp script in themes include the following tasks -

- Styles Task
- Javascript Task
- Images Task
- Translation Task
- Google Fonts Task
- Bundle Task
- Replace Task
- Git Task
- Build Task
- Release Task

Each of these tasks have their own sub-tasks which conclude the final behavior of this script that is building & releasing for development as well as production.

### Styles Task

_This below is an example task from one of our themes which you may see for SASS to CSS processing._

```
/**
 * Task: `styles`.
 *
 * Compiles Sass, Autoprefixes it and Minifies CSS.
 *
 */
gulp.task( 'styles', ( done ) => {
	config.stylesheets.map( function( file ) {
		return gulp
			.src( file.src, { allowEmpty: true })
			.pipe( plumber( errorHandler ) )
			.pipe( sourcemaps.init() )
			.pipe(
				compass({
					config_file: './config.rb',
					css: 'assets/css',
					sass: 'assets/sass'
				})
			)
			.on( 'error', sass.logError )
			.pipe( sourcemaps.write({ includeContent: false }) )
			.pipe( sourcemaps.init({ loadMaps: true }) )
			.pipe( autoprefixer( config.BROWSERS_LIST ) )
			.pipe( sourcemaps.write( './' ) )
			.pipe( lineec() ) // Consistent Line Endings for non UNIX systems.
			.pipe( gulp.dest( file.dest ) )
			.pipe( filter( '**/*.css' ) ) // Filtering stream to only css files.
			.pipe( mmq({ log: true }) ) // Merge Media Queries only for .min.css version.
			.pipe( browserSync.stream() ) // Reloads style.css if that is enqueued.
			.pipe( rename({ suffix: '.min' }) )
			.pipe( minifycss({ maxLineLen: 10 }) )
			.pipe( lineec() ) // Consistent Line Endings for non UNIX systems.
			.pipe( gulp.dest( file.dest ) )
			.pipe( filter( '**/*.css' ) ) // Filtering stream to only css files.
			.pipe( browserSync.stream() ) // Reloads style.min.css if that is enqueued.
			.pipe(
				notify({
					message: `\n\n✅  ===> STYLES — Stylesheet ${file.src} completed!\n`,
					onLast: true
				})
			);
	});
	done();
});
```

This task processes SASS into CSS stylesheets, although there is alot going than just simple processing. Let's list out all the important parts this task completes.
1. Gets SASS files from variable stored in configs.
2. Injects Compass config before actual processing.
3. Compiles SASS to CSS.
4. Writes sourcemaps for it.
5. Autoprefixes for cross-browser compatibility.
6. Minifies stylesheets and renames main stylesheet with .min.css suffix.
7. Fixes line endings for non-unix systems.
8. Injects CSS to the live server or hot reloads browser via browsersync.

_There is a similar sub-task for creating Right-to-Left stylesheets._
