(
var num=32, depth=15, pad=10, size=512, pattern, player, tri, sin, saw, random1, pulse1, pulse2, pulse3, playButton;
t.stop;
s=Server.local;
s.freeAllBuffers;
x.stop;
Window.closeAll;
s.waitForBoot({
	s.scope;
	t=TempoClock(120/60).permanent_(true);
	pattern = Pbind(
			\instrument, \wt,
			\duty, 0.5,
			\amp, Pbrown(0.1,0.25,0.05)*1/5,
			\pan, Pwhite(-1.0,1.0),
			\atk, 1/64,
			\dec, 0,
			\sus, 1,
			\rel, 1,
			\dur, 1/2,
			\octave, Pwhite(5,7),
			\scale, Scale.minor,
			\degree, Pseq([Pxrand([0,2,4,6],8)++
			Pxrand([1,3,5,7],8)++
			Pxrand([1,3,4,6],8)++
				Pxrand([0,2,4,6],8)], inf)
		);
	w = Window.new("Wavetable", Rect(500,600,430+pad+60,13*depth+pad), false).front;
	w.onClose = {s.freeAll; player.stop; Window.closeAll};
	b = Buffer.alloc(s, size * 2);
	playButton = Button(w, Rect(430, pad, 40, 20))
	.states_([["Play", Color.black, Color.green], ["Stop", Color.black, Color.red]])
	.action_({ |button|
		if(button.value == 1 and: { player.isNil or: { player.isPlaying.not } }) {
			player = pattern.play(t);
		} {
			player.stop;
			button.value = 0;
		};
	});
	m = MultiSliderView(w, Rect(pad/2, pad/2, 421, 13*depth));
	m.background=Color.fromHexString("#9bbc0f");
	m.fillColor=Color.fromHexString("#0f380f");
	m.size(num);
	m.gap_(1);
	m.step_(1/depth);
	m.action_({
		arg q;
		var env, wave;
		env=Env(q.value, 1!q.size, \lin);
		b.loadCollection(env.asSignal(size)
			.asWavetable);
	});
	m.valueAction_(
		Array.fill(32, {
			arg i;
			7+(7.5*((pi/16)*i).sin).round(1)/15
		});
	);
	tri = Button(w, Rect(430, 43+(1.5*pad), 60, 20))
	.states_([["Triangle", Color.white, Color.fromHexString("#9a2257")]])
	.action_({
		var array, env;
		array=[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,15,14,13,12,11,10,9,8,7,6,5,4,3,2,1,0]/15;
		m.valueAction_(array);
		env=Env(array, 1!array.size, \lin);
		b.loadCollection(env.asSignal(size)
			.asWavetable)
	});
	sin = Button(w, Rect(430, 20+(1.5*pad), 60, 20))
	.states_([["Sine", Color.white, Color.fromHexString("#9a2257")]])
	.action_({
		var array, env;
		array=Array.fill(32, {
			arg i;
			7+(7.5*((pi/16)*i).sin).round(1)/15
		});
		m.valueAction_(array);
		env=Env(array, 1!array.size, \lin);
		b.loadCollection(env.asSignal(size)
			.asWavetable)
	});
	saw = Button(w, Rect(430, 66+(1.5*pad), 60, 20))
	.states_([["Saw", Color.white, Color.fromHexString("#9a2257")]])
	.action_({
		var array, env;
		array=[15,15,14,14,13,13,12,12,11,11,10,10,9,9,8,8,7,7,6,6,5,5,4,4,3,3,2,2,1,1,0,0]/15;
		m.valueAction_(array);
		env=Env(array, 1!array.size, \lin);
		b.loadCollection(env.asSignal(size)
			.asWavetable)
	});

	random1 = Button(w, Rect(430, 89+(1.5*pad)+(23*3), 60, 20))
	.states_([["Random", Color.white, Color.fromHexString("#9a2257")]])
	.action_({
		var array, env;
		array=Array.rand(32,0,15)/15;
		m.valueAction_(array);
		env=Env(array, 1!array.size, \lin);
		b.loadCollection(env.asSignal(size)
			.asWavetable)
	});

	pulse1 = Button(w, Rect(430, 89+(1.5*pad), 60, 20))
	.states_([["Pulse 1", Color.white, Color.fromHexString("#9a2257")]])
	.action_({
		var array, env;
		array=((15!16)++(0!16))/15;
		m.valueAction_(array);
		env=Env(array, 1!array.size, \lin);
		b.loadCollection(env.asSignal(size)
			.asWavetable)
	});

	pulse2 = Button(w, Rect(430, 89+(1.5*pad)+23, 60, 20))
	.states_([["Pulse 2", Color.white, Color.fromHexString("#9a2257")]])
	.action_({
		var array, env;
		array=((15!8)++(0!24))/15;
		m.valueAction_(array);
		env=Env(array, 1!array.size, \lin);
		b.loadCollection(env.asSignal(size)
			.asWavetable)
	});

	pulse2 = Button(w, Rect(430, 89+(1.5*pad)+46, 60, 20))
	.states_([["Pulse 3", Color.white, Color.fromHexString("#9a2257")]])
	.action_({
		var array, env;
		array=((15!4)++(0!28))/15;
		m.valueAction_(array);
		env=Env(array, 1!array.size, \lin);
		b.loadCollection(env.asSignal(size)
			.asWavetable)
	});

	SynthDef(\wt, {
		arg freq=440, pan=0, amp=0.3, out=0, atk=0, dec=0, sus=1, rel=1, bufnum=b, gate=1;
		var env, sig;
		sig = Osc.ar(bufnum, freq);
		env = EnvGen.ar(
			Env([0,1,sus,0],
				[atk,dec,rel],
				3
			),
			doneAction:2);
		sig = LeakDC.ar(sig);
		sig = sig*env;
		sig = Pan2.ar(sig, pan);
		sig = sig.round(1/2.pow(8));
		Out.ar(out, sig);
	}).add;
});
)
